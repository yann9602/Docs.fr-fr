---
title: "Héberger ASP.NET Core sur Windows avec IIS"
author: guardrex
description: "Découvrez comment héberger des applications ASP.NET Core sur Windows Server Internet Information Services (IIS)."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 03/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/iis/index
ms.openlocfilehash: 18c7448ad79891d04eca1e939a0aeeabe417bde8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Héberger ASP.NET Core sur Windows avec IIS

Par [Luke Latham](https://github.com/guardrex) et [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

Les systèmes d’exploitation suivants sont pris en charge :

* Windows 7 et ultérieur
* Windows Server 2008 R2 et ultérieur&#8224;

&#8224;D’un point de vue conceptuel, la configuration d’IIS décrite dans ce document s’applique également à l’hébergement d’applications ASP.NET Core sur Nano Server IIS, mais pour obtenir des instructions spécifiques, consultez [ASP.NET Core avec IIS sur Nano Server](xref:tutorials/nano-server).

Le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement [WebListener](xref:fundamentals/servers/weblistener)) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Utilisez le [serveur Kestrel](xref:fundamentals/servers/kestrel).

## <a name="iis-configuration"></a>Configuration d’IIS

Activez le rôle **Serveur Web (IIS)** et établissez des services de rôle.

### <a name="windows-desktop-operating-systems"></a>Systèmes d’exploitation de bureau Windows

Accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran). Ouvrez le groupe **Internet Information Services** et **Outils d’administration Web**. Cochez la case **Console de gestion IIS**. Cochez la case **Services World Wide Web**. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a>Systèmes d’exploitation Windows Server

Pour les systèmes d’exploitation de serveur, utilisez l’Assistant **Ajout de rôles et de fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**. À l’étape **Rôles de serveurs**, cochez la case **Serveur Web (IIS)**.

![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

À l’étape **Services de rôle**, sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut.

![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle Serveur Web (IIS).

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core Windows Server

1. Installez le [bundle d’hébergement .NET Core Windows Server](https://aka.ms/dotnetcore-2-windowshosting) sur le système hôte. Le bundle installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Le module crée le proxy inverse entre IIS et le serveur Kestrel. Si le système n’a pas de connexion Internet, obtenez et installez [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) avant d’installer le bundle d’hébergement .NET Core Windows Server.

2. Redémarrez le système ou exécutez **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes pour prendre en compte une modification de la variable système PATH.

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Quand vous déployez des applications sur un serveur avec [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), installez la dernière version de Web Deploy sur le serveur. Pour installer Web Deploy, utilisez [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenez un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="application-configuration"></a>Configuration d’application

### <a name="enabling-the-iisintegration-components"></a>Activation des composants IISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Un fichier *Program.cs* standard appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour commencer à configurer un hôte. `CreateDefaultBuilder` configure [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et active l’intégration d’IIS en configurant le chemin et le port de base pour le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) :

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Incluez une dépendance envers le package [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) dans les dépendances de l’application. Incorporez l’intergiciel (middleware) d’intégration IIS dans l’application en ajoutant la méthode d’extension *UseIISIntegration* à *WebHostBuilder* :

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

`UseKestrel` et `UseIISIntegration` sont tous deux obligatoires. Le code qui appelle `UseIISIntegration` n’affecte pas la portabilité du code. Si l’application n’est pas exécutée derrière IIS (par exemple si l’application est exécutée directement sur Kestrel), `UseIISIntegration` n’effectue pas d’opérations.

---

Pour plus d’informations sur l’hébergement, consultez [Hébergement dans ASP.NET Core](xref:fundamentals/hosting).

### <a name="iis-options"></a>Options IIS

Pour configurer les options IIS, incluez une configuration de service pour `IISOptions` dans `ConfigureServices` :

```csharp
services.Configure<IISOptions>(options => 
{
    ...
});
```

| Option                         | Par défaut | Paramètre |
| ------------------------------ | ------- | ------- |
| `AutomaticAuthentication`      | `true`  | Si `true`, l’intergiciel (middleware) d’authentification définit `HttpContext.User` et répond aux questions génériques. Si `false`, l’intergiciel (middleware) d’authentification fournit uniquement une identité (`HttpContext.User`) et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |
| `ForwardClientCertificate`     | `true`  | Si la valeur est `true` et que l’en-tête de requête `MS-ASPNETCORE-CLIENTCERT` est présent, `HttpContext.Connection.ClientCertificate` est renseigné. |

### <a name="webconfig"></a>web.config

Le fichier *web.config* sert principalement à configurer le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Il peut éventuellement fournir d’autres paramètres de configuration IIS. La création, la transformation et la publication de *web.config* sont gérées par le Kit de développement logiciel (SDK) web .NET Core (`Microsoft.NET.Sdk.Web`). Celui-ci est défini en haut du fichier projet, `<Project Sdk="Microsoft.NET.Sdk.Web">`. Pour empêcher le Kit SDK de transformer le fichier *web.config*, ajoutez la propriété **\<IsTransformWebConfigDisabled>** au fichier projet avec le paramètre `true` :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Si un fichier *web.config* se trouve dans le projet, il est transformé avec le *processPath* et les *arguments* corrects pour configurer le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), puis il est déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure). La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

### <a name="webconfig-location"></a>Emplacement du fichier web.config

Les applications .NET Core sont hébergées par le biais d’un proxy inverse entre IIS et le serveur Kestrel. Pour créer le proxy inverse, le fichier *web.config* doit être présent dans le chemin de racine de contenu (généralement le chemin de base de l’application) de l’application déployée, qui est le chemin physique du site web fourni à IIS. Le fichier *web.config* est nécessaire à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Il existe des fichiers sensibles sur le chemin physique de l’application, notamment des sous-dossiers, tels que *\<nom_assembly>.runtimeconfig.json*, *\<nom_assembly>.xml* (commentaires de documentation XML) et *\<nom_assembly>.deps.json*. Quand le fichier *web.config* est présent et configure le site, IIS empêche l’utilisation de ces fichiers sensibles. **Par conséquent, le fichier *web.config* ne doit jamais être accidentellement renommé ou supprimé du déploiement.**

## <a name="create-the-iis-website"></a>Créer le site web IIS

1. Sur le système IIS cible, créez un dossier pour contenir les dossiers et fichiers publiés de l’application, qui sont décrits dans [Structure de répertoires](xref:host-and-deploy/directory-structure).

2. Dans le dossier, créez un dossier *logs* où stocker les journaux stdout quand la journalisation stdout est activée. Si l’application est déployée avec un dossier *logs* dans la charge utile, ignorez cette étape. Pour obtenir des instructions sur la façon de créer le dossier *logs* à l’aide de MSBuild, consultez la rubrique [Structure de répertoires](xref:host-and-deploy/directory-structure).

3. Dans **IIS Manager**, créez un site web. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application. Spécifiez la configuration de **Liaison** et créez le site web.

4. Sélectionnez **Aucun code managé** pour le pool d’applications. ASP.NET Core s’exécute dans un processus séparé et gère l’exécution.

5. Ouvrez la fenêtre **Ajouter un site Web**.

   ![Sélectionnez « Ajouter un site web » dans le menu contextuel Sites.](index/_static/add-website-context-menu-ws2016.png)

6. Configurez le site web.

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](index/_static/add-website-ws2016.png)

7. Dans le panneau **Pools d’applications**, ouvrez la fenêtre **Modifier le pool d’applications** en cliquant avec le bouton droit sur le pool d’applications du site web et en sélectionnant **Paramètres de base...** dans le menu contextuel.

   ![Sélectionnez Paramètres de base dans le menu contextuel du pool d’applications.](index/_static/apppools-basic-settings-ws2016.png)

8. Affectez la valeur **Aucun code managé** à **Version du CLR .NET**.

   ![Définissez Aucun code managé pour la Version du CLR .NET.](index/_static/edit-apppool-ws2016.png)
     
    Remarque : L’affectation de la valeur **Aucun code managé** à **Version du CLR .NET** est facultative. ASP.NET Core ne repose pas sur le chargement du CLR de bureau.

9. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications (**Modèle de processus** > **Identité**) **ApplicationPoolIdentity** est remplacée par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et aux autres ressources nécessaires.
   
## <a name="deploy-the-app"></a>Déployer l’application

Déployez l’application sur le dossier créé sur le système IIS cible. [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) est le mécanisme recommandé pour le déploiement.

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés. Pour libérer les fichiers verrouillés dans un déploiement, arrêtez le pool d’applications :

* Manuellement dans le Gestionnaire des services IIS sur le serveur.
* En utilisant Web Deploy et en référençant `Microsoft.NET.Sdk.Web` dans le fichier projet. Un fichier *app_offline.htm* est placé à la racine du répertoire de l’application web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#appofflinehtm).
* Utilisez PowerShell pour arrêter et redémarrer le pool d’applications (cette opération requiert PowerShell version 5 ou ultérieure) :
  ```PowerShell
  $webAppPoolName = 'APP_POOL_NAME'

  # Stop the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
      Stop-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Stopped
  }

  # Provide script commands here to deploy the app

  # Restart the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
      Start-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Started
  }
  ```

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio

Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Si le fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio.

![Boîte de dialogue Publier](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) peut également être utilisé en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy

Utilisez la méthode de votre choix, telle que Xcopy, Robocopy ou PowerShell, pour déplacer l’application vers le système hôte. Les utilisateurs de Visual Studio peuvent utiliser les [Exemples de publication](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md).

## <a name="browse-the-website"></a>Parcourir le site web

![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](index/_static/browsewebsite.png)

## <a name="data-protection"></a>Protection des données

Protection des données est utilisée par plusieurs intergiciels ASP.NET, notamment ceux utilisés lors de l’authentification. Même si les API de protection des données ne sont pas appelées à partir du code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un magasin de clés persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le porte-clés est stocké en mémoire, au redémarrage de l’application :

* Tous les jetons d’authentification de formulaires deviennent non valides. 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* Toutes les données protégées par le porte-clés ne peuvent plus être déchiffrées.

Pour configurer la protection des données sous IIS, adoptez **l’une** des approches suivantes :

### <a name="create-a-data-protection-registry-hive"></a>Créer une ruche de Registre de Protection des données

Les clés de Protection des données utilisées par les applications ASP.NET sont stockées dans des ruches de Registre externes aux applications. Afin de conserver les clés pour une application donnée, créez une ruche de Registre pour le pool d’applications.

Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre spéciale dans le Registre HKLM, gérée par liste de contrôle d’accès uniquement pour le compte du processus Worker. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

Dans les scénarios de batterie de serveurs web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son porte-clés de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vérifiez que les autorisations de fichiers pour ce type de partage sont limitées au compte Windows sous lequel s’exécute l’application. En outre, un certificat X509 peut être utilisé pour protéger les clés au repos. Mettez en œuvre un mécanisme permettant aux utilisateurs de charger des certificats : placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d’informations, consultez [Configuration de la Protection des données](xref:security/data-protection/configuration/overview).

### <a name="configure-the-iis-application-pool-to-load-the-user-profile"></a>Configurer le pool d’applications IIS pour charger le profil utilisateur

Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Définissez Charger le profil utilisateur sur `True`. Cela permet de stocker les clés sous le répertoire du profil utilisateur et de les protéger à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications.

### <a name="use-the-file-system-as-a-key-ring-store"></a>Utiliser le système de fichiers comme magasin de porte-clés

Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le porte-clés et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin racine approuvé.

Quand vous utilisez IIS dans une batterie de serveurs web :

* Utilisez un partage de fichiers accessible par tous les ordinateurs.
* Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

### <a name="set-a-machine-wide-policy-for-data-protection"></a>Définir une stratégie au niveau de l’ordinateur pour la protection des données

Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d’informations, consultez la documentation de [protection des données](xref:security/data-protection/index).

## <a name="configuration-of-sub-applications"></a>Configuration des sous-applications

Les sous-applications ajoutées sous l’application racine ne doivent pas inclure le module ASP.NET Core en tant que gestionnaire. Si le module est ajouté en guise de gestionnaire dans le fichier *web.config* d’une sous-application, une erreur 500.19 (erreur interne du serveur) référençant le fichier de configuration défectueux est reçue au moment de la tentative d’accès à la sous-application. L’exemple suivant montre le contenu d’un fichier *web.config* publié pour une sous-application ASP.NET Core :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Si vous hébergez une sous-application non-ASP.NET Core sous une application ASP.NET Core, supprimez explicitement le gestionnaire hérité dans le fichier *web.config* de la sous-application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore"/>
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Pour plus d’informations sur la configuration du module ASP.NET Core, consultez la rubrique [Introduction au module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) et les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est influencée par la section **\<system.webServer>** de *web.config* pour les fonctionnalités d’IIS qui s’appliquent à une configuration de proxy inverse. Si IIS est configuré au niveau du système pour utiliser la compression dynamique, ce paramètre peut être désactivé pour une application avec l’élément **\<urlCompression>** dans le fichier *web.config* de l’application. Pour plus d’informations, consultez les [informations de référence sur configuration de \<system.webServer>](https://docs.microsoft.com/iis/configuration/system.webServer/), les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et [Utilisation des modules IIS avec ASP.NET Core](xref:host-and-deploy/iis/modules). S’il est nécessaire de définir des variables d’environnement pour des applications qui s’exécutent dans des pools d’applications isolés (prise en charge sur IIS 10.0+), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Les sections de configuration des applications ASP.NET Framework dans *web.config* ne sont pas utilisées par les applications ASP.NET Core pour la configuration :

* **\<system.web>**
* **\<appSettings>**
* **\<connectionStrings>**
* **\<location>**

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools d'applications

Quand vous hébergez plusieurs sites web sur un même système, isolez les applications les unes des autres en exécutant chaque application dans son propre pool d’applications. La boîte de dialogue **Ajouter un site Web** d’IIS applique cette configuration par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications**. Un pool d’applications est créé avec le nom du site quand le site web est ajouté.

## <a name="application-pool-identity"></a>Identité du pool d’applications

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0+, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, vérifiez que **l’Identité** est configurée pour utiliser **ApplicationPoolIdentity** :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateur Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

2. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés**.

3. Sous l’onglet **Sécurité**, sélectionnez le bouton **Modifier**, puis le bouton **Ajouter**.

4. Sélectionnez le bouton **Emplacements**, puis vérifiez que le système est sélectionné.

5. Entrez **IIS AppPool\DefaultAppPool** dans la zone de texte **Entrez les noms d’objets à sélectionner**.

   ![Boîte de dialogue de sélection d’utilisateurs ou de groupes pour le dossier d’application](index/_static/select-users-or-groups-1.png)

6. Sélectionnez le bouton **Vérifier les noms**. Sélectionnez **OK**.

   ![Boîte de dialogue de sélection d’utilisateurs ou de groupes pour le dossier d’application](index/_static/select-users-or-groups-2.png)

Vous pouvez également effectuer cette opération par le biais d’une invite de commandes à l’aide de l’outil **ICACLS** :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Résoudre les problèmes liés à ASP.NET Core sur IIS](xref:host-and-deploy/iis/troubleshoot)
* [Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)
* [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [Informations de référence sur la configuration du Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Utilisation de modules IIS avec ASP.NET Core](xref:host-and-deploy/iis/modules)
* [Présentation d’ASP.NET Core](../index.md)
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque Microsoft TechNet : Windows Server](https://docs.microsoft.com/windows-server/windows-server-versions)
