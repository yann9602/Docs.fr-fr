---
title: "À l’aide des modules IIS avec ASP.NET Core"
author: guardrex
description: "Document de référence décrivant les modules IIS actifs et inactifs pour les applications ASP.NET Core."
keywords: "ASP.NET Core, iis, module, proxy inversé"
ms.author: riande
manager: wpickett
ms.date: 03/08/2017
ms.topic: article
ms.assetid: 492b3a7e-04c5-461b-b96a-38ecee5c64bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/iis-modules
ms.openlocfilehash: 353cd4c18cb2708f2dece5ba2b5271f452379d52
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="using-iis-modules-with-aspnet-core"></a>À l’aide des Modules IIS avec ASP.NET Core

Par [Luke Latham](https://github.com/GuardRex)

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
**Document par défaut**<br>`DefaultDocumentModule` | Non | [Intergiciel de fichiers par défaut](xref:fundamentals/static-files#serving-a-default-document)
**Authentification Digest**<br>`DigestAuthenticationModule` | Oui | 
**Exploration des répertoires**<br>`DirectoryListingModule` | Non | [Intergiciel (middleware) exploration de répertoire](xref:fundamentals/static-files#enabling-directory-browsing)
**Compression dynamique**<br>`DynamicCompressionModule` | Oui | [Intergiciel (middleware) de compression des réponses](xref:performance/response-compression)
**Suivi**<br>`FailedRequestsTracingModule` | Oui | [Enregistrement d’ASP.NET Core](xref:fundamentals/logging#the-tracesource-provider)
**La mise en cache du fichier**<br>`FileCacheModule` | Non | [Intergiciel (middleware) de mise en cache des réponses](xref:performance/caching/middleware)
**Mise en cache HTTP**<br>`HttpCacheModule` | Non | [Intergiciel (middleware) de mise en cache des réponses](xref:performance/caching/middleware)
**Journalisation HTTP**<br>`HttpLoggingModule` | Oui | [Enregistrement d’ASP.NET Core](xref:fundamentals/logging)<br>Implémentations : [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)
**Redirection HTTP**<br>`HttpRedirectionModule` | Oui | [Intergiciel (middleware) de réécriture d’URL](xref:fundamentals/url-rewriting)
**Authentification par mappage de certificat Client IIS**<br>`IISCertificateMappingAuthenticationModule` | Oui | 
**Restrictions IP et de domaine**<br>`IpRestrictionModule` | Oui | 
**Filtres ISAPI**<br>`IsapiFilterModule` | Oui | [Intergiciel (middleware)](xref:fundamentals/middleware)
**ISAPI**<br>`IsapiModule` | Oui | [Intergiciel (middleware)](xref:fundamentals/middleware)
**Prise en charge du protocole**<br>`ProtocolSupportModule` | Oui | 
**Filtrage des demandes**<br>`RequestFilteringModule` | Oui | [Intergiciel (middleware) réécriture d’URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule)
**Observateur de demandes**<br>`RequestMonitorModule` | Oui | 
**Réécriture d’URL**<br>`RewriteModule` | Yes† | [Intergiciel (middleware) de réécriture d’URL](xref:fundamentals/url-rewriting)
**Fichiers Include côté serveur**<br>`ServerSideIncludeModule` | Non | 
**Compression statique**<br>`StaticCompressionModule` | Non | [Intergiciel (middleware) de compression des réponses](xref:performance/response-compression)
**Contenu statique**<br>`StaticFileModule` | Non | [Intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files)
**Mise en cache de jeton**<br>`TokenCacheModule` | Oui | 
**La mise en cache URI**<br>`UriCacheModule` | Oui | 
**Autorisation URL**<br>`UrlAuthorizationModule` | Oui | [Identité de ASP.NET Core](xref:security/authentication/identity)
**Authentification Windows**<br>`WindowsAuthenticationModule` | Oui | 

†The Module de réécriture d’URL `isFile` et `isDirectory` ne fonctionnent pas avec les applications ASP.NET Core en raison de modifications dans [structure de répertoires](xref:hosting/directory-structure).

## <a name="managed-modules"></a>Modules managés
Module | .NET core actif | Option ASP.NET Core
--- | :---: | ---
AnonymousIdentification | Non | 
DefaultAuthentication | Non | 
FileAuthorization | Non | 
FormsAuthentication | Non | [Intergiciel (middleware) de cookie d’authentification](xref:security/authentication/cookie)
OutputCache | Non | [Intergiciel (middleware) de mise en cache des réponses](xref:performance/caching/middleware)
Profil | Non | 
RoleManager | Non | 
ScriptModule-4.0 | Non | 
Session | Non | [Intergiciel (middleware) de session](xref:fundamentals/app-state)
UrlAuthorization | Non | 
UrlMappingsModule | Non | [Intergiciel (middleware) de réécriture d’URL](xref:fundamentals/url-rewriting)
UrlRoutingModule-4.0 | Non | [Identité de ASP.NET Core](xref:security/authentication/identity)
WindowsAuthentication | Non | 

## <a name="iis-manager-application-changes"></a>Modification de l’application Gestionnaire des services Internet
Lorsque vous utilisez le Gestionnaire des services Internet pour configurer les paramètres, vous modifiez directement le *web.config* fichier de l’application. Si vous déployez votre application et que vous incluez *web.config*, toutes les modifications apportées à IIS Manager seront remplacées par la version déployée *fichier web.config*. Par conséquent, si vous apportez des modifications sur le serveur *web.config* de fichiers, copiez la mise à jour *web.config* fichier à votre projet local immédiatement.

## <a name="disabling-iis-modules"></a>La désactivation des modules IIS
Si vous avez un module IIS configuré au niveau du serveur que vous voulez désactiver pour une application, vous pouvez le faire avec un ajout à votre *web.config* fichier. Conservez le module en place et désactiver à l’aide d’un paramètre de configuration (si disponible) ou supprimez le module de l’application.

### <a name="module-deactivation"></a>Désactivation du module
Nombre de modules offre un paramètre de configuration qui vous permet de les désactiver sans les supprimer à partir de l’application. Il s’agit de la façon la plus simple et plus rapide pour désactiver un module. Par exemple, si vous souhaitez désactiver le Module de réécriture d’URL IIS, utilisez le `<httpRedirect>` comme indiqué ci-dessous. Pour plus d’informations sur la désactivation de modules avec les paramètres de configuration, suivez les liens dans le *des éléments enfants* section de [IIS `<system.webServer>` ](https://docs.microsoft.com/iis/configuration/system.webServer/).

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

### <a name="module-removal"></a>Retrait du module
Si vous optez pour supprimer un module avec un paramètre dans *web.config*, vous devez déverrouiller le module et déverrouiller le `<modules>` section de *web.config* première. Les étapes sont décrites ci-dessous :

1. Déverrouiller le module au niveau du serveur. Cliquez sur le serveur IIS dans le Gestionnaire IIS **connexions** barre latérale. Ouvrez le **Modules** dans les **IIS** zone. Cliquez sur le module dans la liste. Dans le **Actions** encadré à droite, cliquez sur **Unlock**. Déverrouiller autant de modules que vous souhaitez supprimer avec *web.config* plus tard.

2. Déployer votre application sans un `<modules>` section *web.config*. Si vous déployez une application avec un *web.config* contenant le `<modules>` section sans avoir déverrouillé la section tout d’abord dans le Gestionnaire des services Internet, le Gestionnaire de Configuration lève une exception lorsque vous essayez de déverrouiller la section. Par conséquent, de déployer votre application sans un `<modules>` section.

3. Déverrouiller le `<modules>` section de *web.config*. Dans le **connexions** barre latérale, cliquez sur le site Web dans **Sites**. Dans le **gestion** zone, ouvrez le **l’éditeur de Configuration**. Utilisez les contrôles de navigation pour sélectionner le `system.webServer/modules` section. Dans le **Actions** encadré à droite, cliquez sur **Unlock** la section.

4. À ce stade, vous serez en mesure d’ajouter un `<modules>` section à votre *web.config* de fichiers avec un `<remove>` élément à supprimer le module de l’application. Vous pouvez ajouter plusieurs `<remove>` éléments à supprimer de plusieurs modules. N’oubliez pas que si vous apportez *web.config* modifications sur le serveur pour les rendre immédiatement dans le projet localement. Retrait d’un module de cette manière n’affecte pas votre utilisation du module avec d’autres applications sur le serveur.

  ```xml
  <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
  </configuration>
  ```

Pour une installation d’IIS avec les modules par défaut installé, vous pouvez utiliser les éléments suivants `<module>` section pour supprimer les modules par défaut.

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

Vous pouvez également supprimer un module IIS avec *Appcmd.exe*. Fournir le `MODULE_NAME` et `APPLICATION_NAME` dans la commande ci-dessous :

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Voici comment supprimer le `DynamicCompressionModule` à partir du Site Web par défaut :

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimal-module-configuration"></a>Configuration du module minimale
Les modules uniquement requis pour exécuter une application ASP.NET Core sont le Module d’authentification anonyme et le Module de base ASP.NET.

![Ouvrir le Gestionnaire des services Internet aux Modules avec la configuration de modules minimale indiquée](iis-modules/_static/modules.png)

## <a name="resources"></a>Ressources
* [Publication dans IIS](xref:publishing/iis)
* [Vue d’ensemble des Modules IIS](https://docs.microsoft.com/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Personnalisation de IIS 7.0 rôles et des Modules](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS`<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/)
