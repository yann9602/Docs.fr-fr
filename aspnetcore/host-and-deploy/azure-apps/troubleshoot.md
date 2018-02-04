---
title: "Résoudre les problèmes de base ASP.NET sur Azure App Service"
author: guardrex
description: "Découvrez comment diagnostiquer les problèmes liés aux déploiements ASP.NET Core sur Azure App Service."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 144af8e93bb935d07fd064d5f45b40faea4a2664
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Résoudre les problèmes de base ASP.NET sur Azure App Service

Par [Luke Latham](https://github.com/guardrex)

Cet article fournit des instructions sur la façon de diagnostiquer un ASP.NET Core problème de démarrage d’application à l’aide des outils de diagnostic du Service d’applications Azure. Pour obtenir des conseils de dépannage supplémentaires, consultez [vue d’ensemble des diagnostics du Service d’applications Azure](/azure/app-service/app-service-diagnostics) et [Comment : surveiller les applications dans Azure App Service](/azure/app-service/web-sites-monitor) dans la documentation sur Azure.

## <a name="app-startup-errors"></a>Erreurs de démarrage d’application

**502.5 Échec du processus**  
Le processus de travail échoue. L’application ne démarre pas.

Le [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) tente de démarrer le processus de travail, mais il ne peut pas démarrer. Examiner le journal des événements Application souvent permet de résoudre les problèmes de ce type de problème. Accéder au journal est expliquée dans la [journal des événements Application](#application-event-log) section.

Le *502.5 Échec d’un processus* page d’erreur est retournée lorsqu’une application mal configurée entraîne l’échec du processus de travail :

![Fenêtre de navigateur affichant la page d’échec d’un processus 502.5](troubleshoot/_static/process-failure-page.png)

**Erreur de serveur interne 500**  
L’application démarre, mais une erreur empêche le serveur de répondre à la demande.

Cette erreur se produit dans le code de l’application lors du démarrage ou lors de la création d’une réponse. La réponse ne peut contenir aucun contenu ou la réponse ne peut apparaître comme un *500 Erreur interne au serveur* dans le navigateur. Le journal des événements indique généralement que l’application a démarré normalement. Du point de vue du serveur, qui est correct. L’application n’a pas démarré, mais il ne peut pas générer une réponse valide. [Exécuter l’application dans la console Kudu](#run-the-app-in-the-kudu-console) ou [activer le journal de stdout ASP.NET Core Module](#aspnet-core-module-stdout-log) pour résoudre le problème.

## <a name="troubleshoot-app-startup-errors"></a>Résoudre les erreurs de démarrage d’application

### <a name="application-event-log"></a>Journal des événements de l'application

Pour accéder au journal des événements Application, utilisez le **diagnostiquer et résoudre les problèmes** panneau dans le portail Azure :

1. Dans le portail Azure, ouvrez le panneau de l’application dans le **des Services d’application** panneau.
1. Sélectionnez le **diagnostiquer et résoudre les problèmes** panneau.
1. Sous **sélectionner une catégorie de problème**, sélectionnez le **Web application vers le bas** bouton.
1. Sous **les Solutions suggérées**, ouvrez le volet de **journaux des événements Application ouvrir**. Sélectionnez le **ouvrir les fichiers journaux des événements Application** bouton.
1. Examinez la dernière erreur fournie par le *IIS AspNetCoreModule* dans les **Source** colonne.

Une alternative à l’utilisation de la **diagnostiquer et résoudre les problèmes** panneau consiste à examiner le fichier journal des événements Application directement à l’aide de [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Sélectionnez le **outils avancés** panneau dans le **outils de développement** zone. Sélectionnez le **accédez&rarr;**  bouton. La console Kudu s’ouvre dans une fenêtre ou un nouvel onglet de navigateur.
1. À l’aide de la barre de navigation en haut de la page, ouvrez **console de débogage** et sélectionnez **CMD**.
1. Ouvrez le **LogFiles** dossier.
1. Sélectionnez l’icône de crayon à côté du *eventlog.xml* fichier.
1. Examinez le journal. Faites défiler vers le bas du journal pour voir les événements les plus récents.

### <a name="run-the-app-in-the-kudu-console"></a>Exécuter l’application dans la console Kudu

De nombreuses erreurs de démarrage ne produisent pas des informations utiles dans le journal des événements. Vous pouvez exécuter l’application le [Kudu](https://github.com/projectkudu/kudu/wiki) Console de l’exécution à distance pour détecter l’erreur :

1. Sélectionnez le **outils avancés** panneau dans le **outils de développement** zone. Sélectionnez le **accédez&rarr;**  bouton. La console Kudu s’ouvre dans une fenêtre ou un nouvel onglet de navigateur.
1. À l’aide de la barre de navigation en haut de la page, ouvrez **console de débogage** et sélectionnez **CMD**.
1. Ouvrir les dossiers pour le chemin d’accès **site** > **wwwroot**.
1. Dans la console, exécutez l’application en exécutant l’assembly de l’application avec *dotnet.exe*. Dans la commande suivante, remplacez le nom de l’assembly de l’application pour `<assembly_name>`:
   ```console
   dotnet .\<assembly_name>.dll
   ```
1. La console à partir de l’application, en affichant les erreurs, de sortie est dirigée vers la console Kudu.

### <a name="aspnet-core-module-stdout-log"></a>Journal de stdout de Module de base ASP.NET

Le Module de base ASP.NET stdout souvent enregistre les messages d’erreur utile introuvables dans le journal des événements. Pour activer et afficher les journaux de stdout :

1. Accédez à la **diagnostiquer et résoudre les problèmes** panneau dans le portail Azure.
1. Sous **sélectionner une catégorie de problème**, sélectionnez le **Web application vers le bas** bouton.
1. Sous **les Solutions suggérées** > **activer la Redirection de journal de Stdout**, sélectionnez le bouton pour **Kudu ouvrir la Console pour modifier le fichier Web.Config**.
1. Dans le Kudu **Diagnostic Console**, ouvrez les dossiers pour le chemin d’accès **site** > **wwwroot**. Faites défiler jusqu'à révéler le *web.config* fichier au bas de la liste.
1. Cliquez sur l’icône de crayon en regard du *web.config* fichier.
1. Définissez **stdoutLogEnabled** à `true` et modifiez le **stdoutLogFile** chemin d’accès à : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **enregistrer** pour enregistrer les mises à jour *web.config* fichier.
1. Effectuer une demande à l’application.
1. Revenez au portail Azure. Sélectionnez le **outils avancés** panneau dans le **outils de développement** zone. Sélectionnez le **accédez&rarr;**  bouton. La console Kudu s’ouvre dans une fenêtre ou un nouvel onglet de navigateur.
1. À l’aide de la barre de navigation en haut de la page, ouvrez **console de débogage** et sélectionnez **CMD**.
1. Sélectionnez le **LogFiles** dossier.
1. Inspecter le **modifié** colonne et sélectionnez l’icône de crayon pour modifier le stdout du journal avec la date de dernière modification.
1. Lorsque le fichier journal s’ouvre, l’erreur s’affiche.

**Important !** Désactiver la journalisation de stdout lors de la résolution des problèmes sont terminée.

1. Dans le Kudu **Diagnostic Console**, retournez dans le chemin d’accès **site** > **wwwroot** pour faire apparaître le *web.config* fichier. Ouvrez le **web.config** le fichier à nouveau en sélectionnant l’icône crayon.
1. Définissez **stdoutLogEnabled** à `false`.
1. Sélectionnez **enregistrer** pour enregistrer le fichier.

> [!WARNING]
> Échec pour désactiver le journal de stdout risque d’échec de l’application ou le serveur. Il existe aucune limite quant à la taille du fichier journal ou le nombre de fichiers journaux créés.
>
> Pour la routine de journalisation dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et la faire pivoter des journaux. Pour plus d’informations, consultez [modules fournisseurs d’informations de tiers](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="common-startup-errors"></a>Erreurs courantes de démarrage 

Consultez le [référence des erreurs commune ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference). La plupart des problèmes courants qui empêchent le démarrage de l’application est traitée dans la rubrique de référence.

## <a name="process-dump-for-a-slow-or-hanging-app"></a>Vidage de processus pour une application lente ou bloqué

Lorsqu’une application répond lentement ou se bloque sur une requête, consultez [résoudre les problèmes de performances du application web lente dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation) des conseils de débogage.

## <a name="remote-debugging"></a>Débogage distant

Consultez [section d’applications web de dépanner une application web dans Azure App Service à l’aide de Visual Studio de débogage distant](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) dans la documentation sur Azure.

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) fournit les données de télémétrie des applications hébergées dans Azure App Service, notamment la journalisation des erreurs et les fonctions de rapport. Application Insights peuvent uniquement générer des rapports sur les erreurs qui surviennent après le démarrage de l’application lors de la disponibilité des fonctionnalités de journalisation de l’application. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="monitoring-blades"></a>Analyse des panneaux

Analyse des panneaux offrent une alternative expérience pour les méthodes décrites précédemment dans la rubrique de dépannage. Ces panneaux peut être utilisé pour diagnostiquer les erreurs de la série 500.

Vérifiez que les Extensions de base ASP.NET sont installées. Si les extensions ne sont pas installées, installez-les manuellement :

1. Dans le **outils de développement** section panneau, sélectionnez le **Extensions** panneau.
1. Le **les Extensions ASP.NET Core** doit apparaître dans la liste.
1. Si les extensions ne sont pas installées, sélectionnez le **ajouter** bouton.
1. Choisissez le **les Extensions ASP.NET Core** dans la liste.
1. Sélectionnez **OK** pour accepter les conditions juridiques.
1. Sélectionnez **OK** sur la **ajouter une extension** panneau.
1. Un message contextuel d’information indique que lorsque les extensions installées avec succès.

Si la journalisation de stdout n’est pas activée, procédez comme suit :

1. Dans le portail Azure, sélectionnez le **outils avancés** panneau dans le **outils de développement** zone. Sélectionnez le **accédez&rarr;**  bouton. La console Kudu s’ouvre dans une fenêtre ou un nouvel onglet de navigateur.
1. À l’aide de la barre de navigation en haut de la page, ouvrez **console de débogage** et sélectionnez **CMD**.
1. Ouvrir les dossiers pour le chemin d’accès **site** > **wwwroot** et faites défiler jusqu'à révéler le *web.config* fichier au bas de la liste.
1. Cliquez sur l’icône de crayon en regard du *web.config* fichier.
1. Définissez **stdoutLogEnabled** à `true` et modifiez le **stdoutLogFile** chemin d’accès à : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **enregistrer** pour enregistrer les mises à jour *web.config* fichier.

Passez à activer la journalisation des diagnostics :

1. Dans le portail Azure, sélectionnez le **les journaux de diagnostic** panneau.
1. Sélectionnez le **sur** basculer pour **journalisation des applications (système de fichiers)** et **messages d’erreur détaillés**. Sélectionnez le **enregistrer** bouton en haut du panneau.
1. Pour inclure le suivi des demandes ayant échoué, également appelé enregistrement de l’échec de la demande événement mise en mémoire tampon (FREB), activez la **sur** basculer pour **le suivi des demandes n’a pas pu**. 
1. Sélectionnez le **flux du journal** panneau situé immédiatement sous le **les journaux de diagnostic** panneau dans le portail.
1. Effectuer une demande à l’application.
1. Dans les données de flux de journal, la cause de l’erreur est indiquée.

**Important !** Veillez à désactiver la journalisation de stdout lors de la résolution des problèmes sont terminée. Consultez les instructions dans le [journal stdout de Module de base ASP.NET](#aspnet-core-module-stdout-log) section.

Pour afficher les journaux de suivi des demandes ayant échoué (FREB journaux) :

1. Accédez à la **diagnostiquer et résoudre les problèmes** panneau dans le portail Azure.
1. Sélectionnez **Échec de journaux de suivi demande** à partir de la **prise en charge des outils** zone de la barre latérale.

Voir [Échec de la requête effectue le suivi la section de la journalisation des diagnostics activer pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) et [performances Foire aux questions de l’Application pour les applications Web dans Azure : comment pour activer le suivi des demandes ayant échoué ?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Pour plus d’informations.

Pour plus d’informations, consultez [activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Échec pour désactiver le journal de stdout risque d’échec de l’application ou le serveur. Il existe aucune limite quant à la taille du fichier journal ou le nombre de fichiers journaux créés.
>
> Pour la routine de journalisation dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et la faire pivoter des journaux. Pour plus d’informations, consultez [modules fournisseurs d’informations de tiers](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Introduction à la gestion des erreurs dans ASP.NET Core](xref:fundamentals/error-handling)
* [Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)
* [Résoudre les problèmes d’une application web dans Azure App Service à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Résoudre les erreurs HTTP de « 502 Passerelle incorrecte » et « 503 service non disponible » dans vos applications web Azure](/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Résoudre les problèmes de performances d’application web lente dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Performances de l’application des questions fréquentes pour les applications Web dans Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure vendredi : Azure App Service de Diagnostic et résolution des problèmes d’expérience (vidéo liée à 12 minutes)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
