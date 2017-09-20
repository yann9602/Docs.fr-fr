---
title: "Présentation de l’hébergement et du déploiement - ASP.NET Core"
author: tdykstra
description: "Vue d’ensemble illustrant comment configurer des environnements d’hébergement et y déployer des applications ASP.NET Core."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: f0930c68-4d17-4748-adbf-801e17601eb6
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/index
ms.openlocfilehash: df3c1f0c2768b89c3ea5dc901782170c530a542e
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="hosting-and-deployment-overview-for-aspnet-core-apps"></a>Présentation de l’hébergement et du déploiement pour les applications ASP.NET Core

Voici les principales étapes que vous devez effectuer pour déployer une application ASP.NET Core sur un environnement d’hébergement :

* Publier l’application dans un dossier sur le serveur d’hébergement.
* Configurer un gestionnaire de processus qui démarre l’application lors de l’arrivée des requêtes et la redémarre en cas de blocage ou quand le serveur redémarre.
* Configurer un proxy inverse qui transfère les requêtes à l’application.

## <a name="publish-to-a-folder"></a>Publier dans un dossier 

La commande CLI [dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) compile le code d’application et copie les fichiers nécessaires pour exécuter l’application dans un dossier *publish*. Quand vous déployez à partir de Visual Studio, l’étape `dotnet publish` est effectuée automatiquement avant que les fichiers soient copiés vers la destination du déploiement.

### <a name="folder-contents"></a>Contenu du dossier

Le dossier *publish* contient des fichiers *.exe* et *.dll* pour l’application, ses dépendances et éventuellement le runtime .NET.

Une application .NET Core peut être publiée en tant qu’application *autonome* ou *dépendante du framework*. Si l’application est autonome, les fichiers *.dll* qui contiennent le runtime .NET sont inclus dans le dossier *publish*.  Si l’application dépend du framework, les fichiers du runtime .NET ne sont pas inclus, car l’application a une référence à une version de .NET installée sur l’ordinateur. Le modèle de déploiement par défaut est « dépendante du framework ». Pour plus d’informations, consultez [Déploiement d’applications .NET Core](https://docs.microsoft.com/dotnet/articles/core/deploying/index).

En plus des fichiers *.exe* et *.dll*, le dossier *publish* d’une application ASP.NET Core contient généralement des fichiers de configuration, des ressources statiques et des vues MVC.  Pour plus d’informations, consultez [Structure des répertoires](xref:hosting/directory-structure).

## <a name="set-up-a-process-manager"></a>Configurer un gestionnaire de processus

Une application ASP.NET Core est une application console qui doit être démarrée quand un serveur démarre, et redémarrée après un blocage. Pour automatiser le démarrage et le redémarrage, vous avez besoin d’un gestionnaire de processus. Les gestionnaires de processus les plus courants pour ASP.NET Core sont [Nginx](xref:publishing/linuxproduction) et [Apache](xref:publishing/apache-proxy) sur Linux, et [IIS](xref:publishing/iis) et [Windows Service](xref:hosting/windows-service) sur Windows.

## <a name="set-up-a-reverse-proxy"></a>Configurer un proxy inverse

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si votre application utilise le serveur web [Kestrel](xref:fundamentals/servers/kestrel), vous pouvez utiliser [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy) ou [IIS](xref:publishing/iis) comme serveur proxy inverse. Un serveur proxy inverse reçoit des requêtes HTTP à partir d’Internet et les transfère à Kestrel après une gestion préliminaire. Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Si votre application utilise le serveur web [Kestrel](xref:fundamentals/servers/kestrel) et qu’elle sera exposée à Internet, vous devez utiliser [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy) ou [IIS](xref:publishing/iis) comme serveur proxy inverse. Un serveur proxy inverse reçoit des requêtes HTTP à partir d’Internet et les transfère à Kestrel après une gestion préliminaire. La principale raison pour utiliser un proxy inverse est la sécurité. Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).

---

## <a name="using-visual-studio-and-msbuild-to-automate-deployment"></a>Utilisation de Visual Studio et MSBuild pour automatiser le déploiement

Le déploiement nécessite souvent des tâches supplémentaires en plus de la copie de la sortie de `dotnet publish` vers un serveur. Par exemple, vous souhaiterez peut-être inclure des fichiers supplémentaires dans le dossier *publish*, ou en exclure certains. Visual Studio utilise MSBuild pour le déploiement web, et vous pouvez personnaliser MSBuild pour effectuer de nombreuses autres tâches pendant le déploiement. Pour plus d’informations, consultez [Profils de publication dans Visual Studio](xref:publishing/web-publishing-vs) et l’ouvrage [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Utilisation de MSBuild et Team Foundation Build).

Vous pouvez déployer directement à partir de Visual Studio sur Azure App Service à l’aide de la [fonctionnalité de publication web](xref:tutorials/publish-to-azure-webapp-using-vs) ou de la [prise en charge intégrée de Git](xref:publishing/azure-continuous-deployment). Visual Studio Team Services prend en charge le [déploiement continu sur Azure App Service](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure).

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur l’utilisation de Docker comme environnement d’hébergement, consultez [Applications ASP.NET Core hôtes dans Docker](xref:publishing/docker).
