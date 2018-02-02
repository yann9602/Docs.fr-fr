---
title: "À l’aide des modules IIS avec ASP.NET Core"
author: guardrex
description: "Document de référence décrivant les modules IIS actifs et inactifs pour les applications ASP.NET Core."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 03/08/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/iis/modules
ms.openlocfilehash: b7c81f2851a932cd12553af4a2655eb9f1f7bc64
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="using-iis-modules-with-aspnet-core"></a>À l’aide des Modules IIS avec ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Les applications ASP.NET Core sont hébergées par IIS dans une configuration de proxy inverse. Certains des modules IIS natifs et tous les modules IIS gérés ne sont pas disponibles pour traiter les demandes pour les applications ASP.NET Core. Dans de nombreux cas, ASP.NET Core offre une alternative aux fonctionnalités des modules natifs et managés d’IIS.

## <a name="native-modules"></a>Modules natifs

Module | .NET core actif | Option ASP.NET Core
--- | :---: | ---
**Authentification anonyme**<br>`AnonymousAuthenticationModule` | Oui | 
**Authentification de base**<br>`BasicAuthenticationModule` | Oui | 
**Authentification par mappage de Certification de client**<br>`CertificateMappingAuthenticationModule` | Oui | 
**CGI**<br>`CgiModule` | Non | 
**Validation de la configuration**<br>`ConfigurationValidationModule` | Oui | 
**Erreurs HTTP**<br>`CustomErrorModule` | Non | [Intergiciel (middleware) Pages de Code état](xref:fundamentals/error-handling#configuring-status-code-pages)
**Journalisation personnalisée**<br>`CustomLoggingModule` | Oui | 
**Document par défaut**<br>`DefaultDocumentModule` | Non | [Intergiciel de fichiers par défaut](xref:fundamentals/static-files#serve-a-default-document)
**Authentification Digest**<br>`DigestAuthenticationModule` | Oui | 
**Exploration des répertoires**<br>`DirectoryListingModule` | Non | [Intergiciel (middleware) exploration de répertoire](xref:fundamentals/static-files#enable-directory-browsing)
**Compression dynamique**<br>`DynamicCompressionModule` | Oui | [Intergiciel (middleware) de compression des réponses](xref:performance/response-compression)
**Suivi**<br>`FailedRequestsTracingModule` | Oui | [Enregistrement d’ASP.NET Core](xref:fundamentals/logging/index#the-tracesource-provider)
**La mise en cache du fichier**<br>`FileCacheModule` | Non | [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
**Mise en cache HTTP**<br>`HttpCacheModule` | Non | [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
**Journalisation HTTP**<br>`HttpLoggingModule` | Oui | [Enregistrement d’ASP.NET Core](xref:fundamentals/logging/index)<br>Implémentations : [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)
**Redirection HTTP**<br>`HttpRedirectionModule` | Oui | [Intergiciel de réécriture d’URL](xref:fundamentals/url-rewriting)
**Authentification par mappage de certificat Client IIS**<br>`IISCertificateMappingAuthenticationModule` | Oui | 
**Restrictions IP et de domaine**<br>`IpRestrictionModule` | Oui | 
**Filtres ISAPI**<br>`IsapiFilterModule` | Oui | [Intergiciel (middleware)](xref:fundamentals/middleware/index)
**ISAPI**<br>`IsapiModule` | Oui | [Intergiciel (middleware)](xref:fundamentals/middleware/index)
**Prise en charge du protocole**<br>`ProtocolSupportModule` | Oui | 
**Filtrage des demandes**<br>`RequestFilteringModule` | Oui | [Intergiciel (middleware) réécriture d’URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule)
**Observateur de demandes**<br>`RequestMonitorModule` | Oui | 
**Réécriture d’URL**<br>`RewriteModule` | Yes† | [Intergiciel de réécriture d’URL](xref:fundamentals/url-rewriting)
**Fichiers Include côté serveur**<br>`ServerSideIncludeModule` | Non | 
**Compression statique**<br>`StaticCompressionModule` | Non | [Intergiciel (middleware) de compression des réponses](xref:performance/response-compression)
**Contenu statique**<br>`StaticFileModule` | Non | [Intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files)
**Mise en cache de jeton**<br>`TokenCacheModule` | Oui | 
**La mise en cache URI**<br>`UriCacheModule` | Oui | 
**Autorisation URL**<br>`UrlAuthorizationModule` | Oui | [Identité de ASP.NET Core](xref:security/authentication/identity)
**Authentification Windows**<br>`WindowsAuthenticationModule` | Oui | 

†The Module de réécriture d’URL `isFile` et `isDirectory` ne fonctionnent pas avec les applications ASP.NET Core en raison de modifications dans [structure de répertoires](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Modules managés

Module | .NET core actif | Option ASP.NET Core
--- | :---: | ---
AnonymousIdentification | Non | 
DefaultAuthentication | Non | 
FileAuthorization | Non | 
FormsAuthentication | Non | [Intergiciel (middleware) de cookie d’authentification](xref:security/authentication/cookie)
OutputCache | Non | [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
Profil | Non | 
RoleManager | Non | 
ScriptModule-4.0 | Non | 
Session | Non | [Intergiciel (middleware) de session](xref:fundamentals/app-state)
UrlAuthorization | Non | 
UrlMappingsModule | Non | [Intergiciel de réécriture d’URL](xref:fundamentals/url-rewriting)
UrlRoutingModule-4.0 | Non | [Identité de ASP.NET Core](xref:security/authentication/identity)
WindowsAuthentication | Non | 

## <a name="iis-manager-application-changes"></a>Modification de l’application Gestionnaire des services Internet

À l’aide du Gestionnaire des services Internet pour configurer les paramètres, le *web.config* fichier de l’application est modifié. Si le déploiement d’une application et en incluant *web.config*, toutes les modifications apportées à IIS Manager sont remplacées par déployé *web.config* fichier. Si des modifications sont apportées au serveur *web.config* de fichiers, copiez la mise à jour *web.config* fichier au projet local immédiatement.

## <a name="disabling-iis-modules"></a>La désactivation des modules IIS

Si un module IIS est configuré au niveau du serveur qui doit être désactivé pour une application, un complément de l’application *web.config* fichier peut désactiver le module. Conservez le module en place et désactiver à l’aide d’un paramètre de configuration (si disponible) ou supprimez le module de l’application.

### <a name="module-deactivation"></a>Désactivation du module

Nombre de modules offre un paramètre de configuration qui leur permet d’être désactivés sans les supprimer à partir de l’application. Il s’agit de la façon la plus simple et plus rapide pour désactiver un module. Par exemple, si je souhaite désactiver le Module de réécriture d’URL IIS, utilisez le `<httpRedirect>` comme indiqué ci-dessous. Pour plus d’informations sur la désactivation de modules avec les paramètres de configuration, suivez les liens dans le *des éléments enfants* section de [IIS `<system.webServer>` ](https://docs.microsoft.com/iis/configuration/system.webServer/).

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

### <a name="module-removal"></a>Retrait du module

Si vous être inscrit pour supprimer un module avec un paramètre dans *web.config*, déverrouillage du module et le déverrouillage du `<modules>` section de *web.config* première. Les étapes sont décrites ci-dessous :

1. Déverrouiller le module au niveau du serveur. Cliquez sur le serveur IIS dans le Gestionnaire IIS **connexions** barre latérale. Ouvrez le **Modules** dans les **IIS** zone. Cliquez sur le module dans la liste. Dans le **Actions** encadré à droite, cliquez sur **Unlock**. Déverrouiller autant de modules sont planifiés à supprimer de *web.config* plus tard.

2. Déployer l’application sans un `<modules>` section *web.config*. Si une application est déployée avec une *web.config* contenant le `<modules>` section sans avoir déverrouillé la section tout d’abord dans le Gestionnaire des services Internet, le Gestionnaire de Configuration lève une exception lors de la tentative de déverrouillage de la section. Par conséquent, déployez l’application sans un `<modules>` section.

3. Déverrouiller le `<modules>` section de *web.config*. Dans le **connexions** barre latérale, cliquez sur le site Web dans **Sites**. Dans le **gestion** zone, ouvrez le **l’éditeur de Configuration**. Utilisez les contrôles de navigation pour sélectionner le `system.webServer/modules` section. Dans le **Actions** encadré à droite, cliquez sur **Unlock** la section.

4. À ce stade, un `<modules>` section peut être ajoutée à la *web.config* de fichiers avec un `<remove>` élément à supprimer le module à partir de l’application. Plusieurs `<remove>` éléments peuvent être ajoutés pour supprimer plusieurs modules. N’oubliez pas que se *web.config* modifications sont apportées sur le serveur pour les rendre immédiatement dans le projet localement. Retrait d’un module de cette manière n’affecte pas l’utilisation du module avec d’autres applications sur le serveur.

  ```xml
  <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
  </configuration>
  ```

Pour une installation d’IIS avec les modules par défaut installé, utilisez ce qui suit `<module>` section pour supprimer les modules par défaut.

```xml
<modules>
  <remove name="CustomErrorModule" />
  <remove name="DefaultDocumentModule" />
  <remove name="DirectoryListingModule" />
  <remove name="HttpCacheModule" />
  <remove name="HttpLoggingModule" />
  <remove name="ProtocolSupportModule" />
  <remove name="RequestFilteringModule" />
  <remove name="StaticCompressionModule" /> 
  <remove name="StaticFileModule" /> 
</modules>
```

Un module IIS peut également être supprimé avec *Appcmd.exe*. Fournir le `MODULE_NAME` et `APPLICATION_NAME` dans la commande ci-dessous :

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Voici comment supprimer le `DynamicCompressionModule` à partir du Site Web par défaut :

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Configuration du module minimale

Les modules uniquement requis pour exécuter une application ASP.NET Core sont le Module d’authentification anonyme et le Module de base ASP.NET.

![Ouvrir le Gestionnaire des services Internet aux Modules avec la configuration de modules minimale indiquée](modules/_static/modules.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Héberger sur Windows avec IIS](xref:host-and-deploy/iis/index)
* [Vue d’ensemble des Modules IIS](https://docs.microsoft.com/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Personnalisation de IIS 7.0 rôles et des Modules](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS`<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/)
