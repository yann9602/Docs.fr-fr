---
title: "Référence de configuration du Module de base ASP.NET"
author: guardrex
description: "Comment configurer le Module de base ASP.NET pour héberger les applications ASP.NET Core."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 03/07/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 7bb7e5b9c821f87e73763f5f5c4f9fbcd751235f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="aspnet-core-module-configuration-reference"></a>Référence de configuration du Module de base ASP.NET

Par [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), et [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Ce document fournit des détails sur la façon de configurer le Module de base ASP.NET pour héberger les applications ASP.NET Core. Pour obtenir une présentation du Module de base ASP.NET et des instructions d’installation, consultez le [vue d’ensemble du Module de base ASP.NET](xref:fundamentals/servers/aspnet-core-module).

## <a name="configuration-via-webconfig"></a>Configuration via le fichier web.config

Le Module de base ASP.NET est configuré via un site ou une application *web.config* de fichiers et a son propre `aspNetCore` section de configuration dans `system.webServer`. Voici un exemple *web.config* de fichiers qui le `Microsoft.NET.Sdk.Web` SDK fournira lorsque le projet est publié pour un [dépendant du framework déploiement](https://docs.microsoft.com/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) avec des espaces réservés pour les `processPath` et `arguments`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="%LAUNCHER_PATH%" 
        arguments="%LAUNCHER_ARGS%" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Le *web.config* exemple ci-dessous concerne une [déploiement autonome](https://docs.microsoft.com/dotnet/articles/core/deploying/#self-contained-deployments-scd) à la [Azure App Service](https://azure.microsoft.com/services/app-service/). Pour plus d’informations, consultez [hôte sous Windows avec IIS](xref:host-and-deploy/iis/index). Consultez [Configuration des sous-applications](xref:host-and-deploy/iis/index#configuration-of-sub-applications) pour une note importante se rapportant à la configuration de *web.config* fichiers dans les applications secondaire.

```xml
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
        stdoutLogEnabled="false" 
        stdoutLogFile="\\?\%home%\LogFiles\stdout" />
  </system.webServer>
</configuration>
```

### <a name="attributes-of-the-aspnetcore-element"></a>Attributs de l’élément aspNetCore

| Attribut | Description |
| --- | --- |
| processPath | <p>Attribut de chaîne requis.</p><p>Chemin d’accès au fichier exécutable qui lance un processus d’écoute les requêtes HTTP. Chemins d’accès relatifs sont pris en charge. Si le chemin d’accès commence par '.', le chemin d’accès est considéré comme étant relatif à la racine du site.</p><p>Il n’existe aucune valeur par défaut.</p> |
| arguments | <p>Attribut de chaîne facultatif.</p><p>Arguments pour l’exécutable spécifié dans **processPath**.</p><p>La valeur par défaut est une chaîne vide.</p> |
| startupTimeLimit | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend l’exécutable à démarrer un processus à l’écoute sur le port. Si cette limite de temps est dépassée, le module va arrêter le processus. Le module va tenter de lancer le processus à nouveau lorsqu’il reçoit une demande de nouveau et continuera d’essayer de redémarrer le processus pour les demandes entrantes suivantes, sauf si l’application ne démarre pas **rapidFailsPerMinute** nombre de la dernière minute propagée fois.</p><p>La valeur par défaut est 120.</p> |
| shutdownTimeLimit | <p>Attribut entier facultatif.</p><p>Durée en secondes, pour laquelle le module attend pour le fichier exécutable d’arrêt en douceur lorsque le *app_offline.htm* fichier est détecté.</p><p>La valeur par défaut est 10.</p> |
| rapidFailsPerMinute | <p>Attribut entier facultatif.</p><p>Spécifie le nombre de fois spécifié par le processus dans **processPath** est autorisé à se bloquer par minute. Si cette limite est dépassée, le module s’arrête de lancement du processus pour le reste de la minute.</p><p>La valeur par défaut est 10.</p> |
| requestTimeout | <p>Attribut de timespan facultatif.</p><p>Spécifie la durée pour laquelle le Module de base ASP.NET attendra une réponse à partir du processus à l’écoute sur % ASPNETCORE_PORT %.</p><p>La valeur par défaut est « 00:02:00 ».</p><p>Le `requestTimeout` doit être spécifié en minutes entières, sinon la valeur par défaut à 2 minutes.</p> |
| stdoutLogEnabled | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, **stdout** et **stderr** pour le processus spécifié dans **processPath** sera redirigé vers le fichier spécifié dans **stdoutLogFile**.</p><p>La valeur par défaut est false.</p> |
| stdoutLogFile | <p>Attribut de chaîne facultatif.</p><p>Spécifie le chemin d’accès relatif ou absolu pour lequel **stdout** et **stderr** du processus spécifié dans **processPath** doivent être enregistrés. Chemins d’accès relatifs sont relatif à la racine du site. N’importe quel chemin d’accès commençant par '.' doit être relatif à la racine du site et tous les autres chemins d’accès seront traités en tant que chemins d’accès absolus. Tous les dossiers du chemin d’accès doivent exister dans l’ordre pour le module créer le fichier journal. L’ID de processus, horodateur (*yyyyMdhms*) et l’extension de fichier (*.log*) avec un trait de soulignement délimiteurs sont ajoutés au dernier segment de la **stdoutLogFile** fourni.</p><p>La valeur par défaut est `aspnetcore-stdout`.</p> |
| forwardWindowsAuthToken | vrai ou faux.</p><p>Si la valeur est true, le jeton est transféré au processus enfant à l’écoute sur % ASPNETCORE_PORT % en tant qu’en-tête 'MS-ASPNETCORE-WINAUTHTOKEN' par demande. Il incombe à ce processus pour appeler CloseHandle sur ce jeton par demande.</p><p>La valeur par défaut est true.</p> |
| disableStartUpErrorPage | vrai ou faux.</p><p>Si la valeur est true, le **502.5 - Échec d’un processus** page est supprimée, et la page de codes de 502 état configurés dans votre *web.config* est prioritaire.</p><p>La valeur par défaut est false.</p> |

### <a name="setting-environment-variables"></a>Définition des variables d’environnement

Le Module de base ASP.NET vous permet de spécifier des variables d’environnement pour le processus spécifié dans le `processPath` attribut en les spécifiant dans une ou plusieurs `environmentVariable` des éléments enfants d’un `environmentVariables` élément de collection sous le `aspNetCore` élément. Variables d’environnement définies dans cette section sont prioritaires sur système variables d’environnement pour le processus.

L’exemple ci-dessous définit deux variables d’environnement. `ASPNETCORE_ENVIRONMENT`configure l’environnement l’application à `Development`. Un développeur peut définir temporairement cette valeur dans la *web.config* fichier afin de forcer le [page d’exception developer](xref:fundamentals/error-handling) à charger lors du débogage d’une exception d’application. `CONFIG_DIR`est un exemple d’une variable d’environnement définie par l’utilisateur, où le développeur a écrit du code qui lit la valeur de démarrage pour former un chemin d’accès afin de charger le fichier de configuration de l’application.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

## <a name="appofflinehtm"></a>app_offline.htm

Si vous placez un fichier portant le nom *app_offline.htm* à la racine d’un répertoire d’application web, le Module de base ASP.NET sera tenter d’arrêt en douceur de l’application et arrêter le traitement des demandes entrantes. Si l’application est en cours d’exécution `shutdownTimeLimit` nombre de secondes, le Module de base ASP.NET va arrêter le processus en cours d’exécution.

Alors que le *app_offline.htm* fichier est présent, le Module de base ASP.NET répondra aux requêtes en renvoyant le contenu de la *app_offline.htm* fichier. Une fois la *app_offline.htm* fichier est supprimé, la prochaine demande de charge de l’application, qui répond aux demandes.

## <a name="start-up-error-page"></a>Page d’erreur de démarrage

Si le Module de base ASP.NET ne parvient pas à lancer le processus principal ou que le début de la procédure principale mais ne parvient pas à l’écoute sur le port configuré, vous verrez une page de codes d’état HTTP 502.5. Pour supprimer cette page et revenir à la page de codes par défaut IIS 502 état, utiliser le `disableStartUpErrorPage` attribut. Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [erreurs HTTP `<httpErrors>` ](https://docs.microsoft.com/iis/configuration/system.webServer/httpErrors/).

![Page d’état 502](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Création d’un journal et la redirection

Le Module de base ASP.NET redirige `stdout` et `stderr` journaux sur le disque si vous définissez la `stdoutLogEnabled` et `stdoutLogFile` les attributs de la `aspNetCore` élément. Tous les dossiers dans le `stdoutLogFile` chemin doit exister dans l’ordre pour le module créer le fichier journal. Une horodatage et extension de fichier est automatiquement ajoutée lorsque le fichier journal est créé. Les journaux n’a pas lieu, à moins que le recyclage de processus/redémarrage se produit. Il incombe à l’hébergeur pour limiter l’espace disque que les connexions à consommer. À l’aide de la `stdout` journal est recommandé uniquement pour résoudre les problèmes de démarrage d’application et non à des fins de journalisation généraux de l’application.

Nom du fichier journal est composé en ajoutant l’ID de processus (PID), horodateur (*yyyyMdhms*) et l’extension de fichier (*.log*) pour le dernier segment de la `stdoutLogFile` chemin d’accès (généralement *stdout* ) délimités par des traits de soulignement. Par exemple si le `stdoutLogFile` chemin d’accès se termine par *stdout*, un journal pour une application avec un PID de 10652 créé sur 8/10/2017 à 12:05:02 a le nom de fichier *stdout_10652_20178101252.log*.

Voici un exemple `aspNetCore` élément configure `stdout` journalisation. Le `stdoutLogFile` chemin d’accès indiqué dans l’exemple est appropriée pour le Service d’applications Azure. Un chemin d’accès local ou un chemin d’accès du partage réseau est acceptable pour la connexion locale. Vérifiez que l’identité de pool d’applications l’utilisateur a l’autorisation d’écrire dans le chemin d’accès fourni.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```
Consultez [Configuration via web.config](#configuration-via-webconfig) pour obtenir un exemple de la `aspNetCore` élément dans le *web.config* fichier.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Configuration de Module ASP.NET Core avec un IIS partagée

Le programme d’installation du Module de base ASP.NET s’exécute avec les privilèges de le **système** compte. Étant donné que le compte système local ont ne modifie pas l’autorisation pour le chemin d’accès de partage qui est utilisée par la Configuration partagée IIS, le programme d’installation sera atteint une erreur d’accès refusé lorsque vous tentez de configurer les paramètres de module dans  *applicationHost.config* sur le partage.

La non prise en charge de la solution de contournement consiste à désactiver la Configuration partagée IIS, exécutez le programme d’installation, exporter la mise à jour *applicationHost.config* au partage de fichiers et de réactiver la Configuration partagée IIS.

## <a name="module-schema-and-configuration-file-locations"></a>Emplacements des fichiers de module, de schéma et de configuration

### <a name="module"></a>Module

**IIS (x86/amd64) :**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64) :**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Schéma

**IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Configuration

**IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

   * .vs\config\applicationHost.config

Vous pouvez rechercher des *aspnetcore.dll* dans les *applicationHost.config* fichier. Pour IIS Express, le *applicationHost.config* fichier n’existe pas par défaut. Le fichier est créé au *{racine de l’application}\.vs\config* lorsque vous démarrez un projet d’application web dans la solution Visual Studio.
