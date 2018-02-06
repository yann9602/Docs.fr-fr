---
title: "Héberger ASP.NET Core sur Azure App Service"
author: guardrex
description: "Découvrez comment héberger des applications ASP.NET Core dans Azure App Service avec des liens vers des ressources utiles."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 34b009d3a298803256c9a06debe6e5026418429a
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="host-aspnet-core-on-azure-app-service"></a>Héberger ASP.NET Core sur Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) est un [service de plateforme de cloud computing Microsoft](https://azure.microsoft.com/) qui permet d’héberger des applications web, notamment ASP.NET Core.

## <a name="useful-resources"></a>Ressources utiles

La documentation d’[Azure Web Apps](/azure/app-service/) contient la documentation, les didacticiels, les exemples, les guides pratiques et d’autres ressources sur les applications Azure. Voici deux didacticiels importants qui abordent l’hébergement d’applications ASP.NET Core :

[Démarrage rapide : créer une application web ASP.NET Core dans Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Utilisez Visual Studio pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Windows.

[Démarrage rapide : créer une application web .NET Core dans App Service sur Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Utilisez la ligne de commande pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Linux.

Les articles suivants sont disponibles dans la documentation d’ASP.NET Core :

[Publier sur Azure avec Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs)  
Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.

[Publier sur Azure avec les outils CLI](xref:tutorials/publish-to-azure-webapp-using-cli)  
Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide du client de ligne de commande Git.

[Déploiement continu sur Azure avec Visual Studio et Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)  
Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu.

[Déploiement continu sur Azure avec VSTS](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure)  
Configurez une build CI pour une application ASP.NET Core, puis créez une version de déploiement continu sur Azure App Service.

## <a name="application-configuration"></a>Configuration d’application

Avec ASP.NET Core 2.0 et ultérieur, trois packages du [métapackage Microsoft.AspNetCore.All](xref:fundamentals/metapackage) fournissent des fonctionnalités de journalisation automatique pour les applications déployées sur Azure App Service :

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) utilise [IHostingStartup](xref:host-and-deploy/ihostingstartup) pour intégrer la fonction d’éclairage ASP.NET Core à Azure App Service. Les fonctionnalités de journalisation ajoutées sont fournies par le package `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) exécute [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pour ajouter des fournisseurs de journalisation de diagnostics Azure App Service dans le package `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fournit des implémentations de journaliseur pour prendre en charge les journaux de diagnostics et les fonctionnalités de streaming de journal Azure App Service.

## <a name="monitoring-and-logging"></a>Surveillance et journalisation

Pour des informations de surveillance, de journalisation et de dépannage, consultez les articles suivants :

[Guide pratique pour surveiller des applications dans Azure App Service](/azure/app-service/web-sites-monitor)  
Découvrez comment consulter les quotas et les métriques des applications et des plans App Service.

[Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Découvrez comment activer et accéder à la journalisation des diagnostics pour les codes d’état HTTP, les requêtes en échec et les activités de serveur web.

[Présentation de la gestion des erreurs dans ASP.NET Core](xref:fundamentals/error-handling)  
Découvrez les approches courantes permettant de gérer les erreurs dans les applications ASP.NET Core.

[Résoudre les problèmes liés à ASP.NET Core sur Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot)  
Découvrez comment diagnostiquer les problèmes de déploiements Azure App Service avec les applications ASP.NET Core.

[Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)  
Découvrez les erreurs de configuration de déploiement courantes dans les applications hébergées par Azure App Service/IIS, ainsi que des conseils de dépannage.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Porte-clés de Protection des données et emplacements de déploiement

Les [clés de Protection des données](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) sont persistantes dans le dossier *%HOME%\ASP.NET\DataProtection-Keys*. Ce dossier est alimenté par le stockage réseau et synchronisé sur tous les ordinateurs hébergeant l’application. Les clés ne sont pas protégées au repos. Ce dossier fournit le porte-clés à toutes les instances d’une application dans un seul emplacement de déploiement. Les emplacements de déploiement distincts, tels que Préproduction et Production, ne partagent pas de porte-clés.

Lors d’une permutation entre les emplacements de déploiement, aucun système utilisant la protection des données ne peut déchiffrer les données stockées à l’aide du porte-clés au sein de l’emplacement précédent. L’intergiciel (middleware) ASP.NET Cookie utilise la protection des données pour protéger ses cookies. Cela entraîne la déconnexion des utilisateurs des applications qui utilisent l’intergiciel ASP.NET Cookie standard. Pour une solution de porte-clés indépendante de l’emplacement, utilisez un fournisseur de porte-clés externe, tel que :

* Stockage Blob Azure
* Azure Key Vault
* Magasin SQL
* Cache Redis

Pour plus d’informations, consultez [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Vue d’ensemble de Web Apps (vidéo de 5 minutes)](/azure/app-service/app-service-web-overview)
* [Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Vue d’ensemble des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics)

Azure App Service sur Windows Server utilise [IIS (Internet Information Services)](https://www.iis.net/). Les rubriques suivantes se rapportent à la technologie IIS sous-jacente :

* [Héberger ASP.NET Core sur Windows avec IIS](xref:host-and-deploy/iis/index)
* [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [Informations de référence sur la configuration du Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Utilisation de modules IIS avec ASP.NET Core](xref:host-and-deploy/iis/modules)
* [Bibliothèque Microsoft TechNet : Windows Server](https://docs.microsoft.com/windows-server/windows-server-versions)
