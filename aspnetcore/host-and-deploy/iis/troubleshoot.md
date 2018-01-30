---
title: "Résoudre les problèmes de base ASP.NET sur IIS"
author: guardrex
description: "Découvrez comment diagnostiquer les problèmes avec les déploiements d’applications ASP.NET Core IIS."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: c68070a9cba5667504d1ad4927b02b73f83e6573
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Résoudre les problèmes de base ASP.NET sur IIS

Par [Luke Latham](https://github.com/guardrex)

Pour diagnostiquer les problèmes liés aux déploiements d’IIS :

* Analysez la sortie du navigateur.
* Examinez le journal des **applications** du système avec **l’Observateur d’événements**.
* Activez la journalisation `stdout`. Le journal du **Module ASP.NET Core** se trouve dans le chemin fourni dans l’attribut *stdoutLogFile* de l’élément `<aspNetCore>` dans *web.config*. Tous les dossiers sur le chemin fourni dans la valeur d’attribut doivent exister dans le déploiement. Définissez *stdoutLogEnabled* à `true`. Applications qui utilisent le le `Microsoft.NET.Sdk.Web` Kit de développement logiciel pour créer le *web.config* de fichiers par défaut le *stdoutLogEnabled* à `false`fournir manuellement le *web.config* de fichiers ou de modifier le fichier afin d’activer `stdout` journalisation.

Utilisez les informations de ces trois sources avec le [les erreurs courantes référencent rubrique](xref:host-and-deploy/azure-iis-errors-reference) pour déterminer le problème. Suivez les conseils de dépannage permettant de résoudre le problème.

Plusieurs erreurs courantes n’apparaissent pas dans le navigateur, le journal des applications et le journal du Module ASP.NET Core tant que les valeurs *startupTimeLimit* (par défaut : 120 secondes) et *startupRetryCount* (par défaut : 2) du module ne sont pas dépassées. Vous devez donc patienter six minutes avant d’en déduire que le module n’a pas réussi à démarrer un processus pour l’application.

Un moyen rapide de déterminer si l’application fonctionne correctement consiste à exécuter l’application directement sur Kestrel. Si l’application a été publiée en tant qu’un [dépendant du framework déploiement](/dotnet/core/deploying/#framework-dependent-deployments-fdd), exécutez `dotnet <assembly_name>.dll` dans le dossier de déploiement, qui est le chemin d’accès physique de IIS à l’application. Si l’application a été publiée en tant qu’un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd), exécutez le fichier exécutable directement à partir d’une invite de commandes, de l’application `<assembly_name>.exe`, dans le dossier de déploiement. Si Kestrel est à l’écoute sur le port par défaut 5000, l’application doit être disponible à l’adresse `http://localhost:5000/`. Si l’application répond généralement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de proxy inverse et moins probable au sein de l’application.

Pour déterminer si le proxy inverse fonctionne correctement consiste à effectuer une demande de fichier statique simple pour une feuille de style, un script ou une image à partir de fichiers statiques de l’application dans *wwwroot* à l’aide de [fichier statique intergiciel (middleware)](xref:fundamentals/static-files). Si l’application peut traiter des fichiers statiques, mais les autres points de terminaison et les vues MVC échouent, le problème est moins probable liés à la configuration de proxy inverse et plus probable que l’application (par exemple, le routage MVC ou 500 Erreur interne au serveur).

Lorsque Kestrel démarre normalement derrière IIS, mais l’application ne s’exécute pas sur le système après avoir correctement exécuté localement, une variable d’environnement peut être ajoutée temporairement à *web.config* pour définir le `ASPNETCORE_ENVIRONMENT` à `Development`. Tant que l’environnement n’est pas substitué dans le démarrage de l’application, la définition de la variable d’environnement permet la [page d’exception developer](xref:fundamentals/error-handling) apparaissent lorsque l’application est exécutée. Définition de la variable d’environnement `ASPNETCORE_ENVIRONMENT` de cette façon est recommandé uniquement pour les serveurs intermédiaires et les tests qui ne sont pas exposés à Internet. Veillez à supprimer la variable d’environnement à partir de la *web.config* lors de la fin du fichier. Pour plus d’informations sur la définition des variables d’environnement via *web.config*, consultez [élément enfant d’environmentVariables d’aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

Dans la plupart des cas, l’activation de la journalisation de l’application aide à résoudre les problèmes liés à l’application ou au proxy inverse. Pour plus d’informations, consultez [Journalisation](xref:fundamentals/logging/index).

Le dernier conseil de dépannage relative aux applications qui ne parviennent pas à exécuter après la mise à niveau le Kit de développement logiciel .NET Core sur les versions de machine ou un package de développement au sein de l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. La plupart de ces problèmes peut être résolue en :

* Suppression de la `bin` et `obj` dossiers dans le projet.
* Met en cache le package de compensation à `%UserProfile%\.nuget\packages\` et `%LocalAppData%\Nuget\v3-cache`.
* La restauration et régénérer le projet.
* Confirmez que le déploiement préalable sur le serveur a été supprimé complètement avant de redéployer l’application.

> [!TIP]
> Un moyen pratique pour effacer les caches de package consiste à exécuter `dotnet nuget locals all --clear` à partir d’une invite de commandes.
> 
> Effacer les caches de package peut également être accompli en utilisant le [nuget.exe](https://www.nuget.org/downloads) outil et l’exécution de la commande `nuget locals all -clear`. *NuGet.exe* n’est pas une installation fournie avec Windows 10 et doivent être obtenues séparément à partir du site Web NuGet.
<!--
> [!TIP]
> A convenient way to clear package caches is to:
>
> * Obtain the *NuGet.exe* tool from [NuGet.org](https://www.nuget.org/).
> * Add the path to *NuGet.exe* to the system PATH.
> * Execute `nuget locals all -clear` from a command prompt.
>
> Alternatively, execute `dotnet nuget locals all --clear` from a command prompt without obtaining *NuGet.exe*. -->

## <a name="additional-resources"></a>Ressources supplémentaires

* [Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)
* [Informations de référence sur la configuration du Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
