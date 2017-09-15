---
title: "Héberger ASP.NET Core sur Windows avec IIS"
author: guardrex
description: "Configuration et déploiement d’applications ASP.NET Core sur Windows Server Internet Information Services (IIS)."
keywords: "ASP.NET Core, internet information services, iis, windows server, bundle d’hébergement, module asp.net core, déploiement web"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: a4449ad3-5bad-410c-afa7-dc32d832b552
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/iis
ms.openlocfilehash: 351f3519643bc88fc3dd1c4fbac1c144c6837523
ms.sourcegitcommit: 0a70706a3814d2684f3ff96095d1e8291d559cc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-windows-with-iis-and-deploy-to-it"></a>Configurer un environnement d’hébergement pour ASP.NET Core sur Windows avec IIS et déployer sur celui-ci

Par [Luke Latham](https://github.com/GuardRex) et [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

Les systèmes d’exploitation suivants sont pris en charge :

* Windows 7 et ultérieur

* Windows Server 2008 R2 et ultérieur&#8224;

&#8224;D’un point de vue conceptuel, la configuration d’IIS décrite dans ce document s’applique également à l’hébergement d’applications ASP.NET Core sur Nano Server IIS, mais pour obtenir des instructions spécifiques, consultez [ASP.NET Core avec IIS sur Nano Server](xref:tutorials/nano-server).

Le [serveur WebListener](xref:fundamentals/servers/weblistener) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Vous devez utiliser le [server Kestrel](xref:fundamentals/servers/kestrel).

## <a name="iis-configuration"></a>Configuration d’IIS

Activez le rôle **Serveur Web (IIS)** et établissez des services de rôle.

### <a name="windows-desktop-operating-systems"></a>Systèmes d’exploitation de bureau Windows

Accédez à **Panneau de configuration > Programmes > Programmes et fonctionnalités > Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran). Ouvrez le groupe **Internet Information Services** et **Outils d’administration Web**. Cochez la case **Console de gestion IIS**. Cochez la case **Services World Wide Web**. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS en fonction de vos besoins.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a>Systèmes d’exploitation Windows Server

Pour les systèmes d’exploitation de serveur, utilisez l’Assistant **Ajout de rôles et de fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**. À l’étape **Rôles de serveurs**, cochez la case **Serveur Web (IIS)**.

![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](iis/_static/server-roles-ws2016.png)

À l’étape **Services de rôle**, sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut.

![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](iis/_static/role-services-ws2016.png)

Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle Serveur Web (IIS).

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core Windows Server

1. Installez le [bundle d’hébergement .NET Core Windows Server](https://aka.ms/dotnetcore.2.0.0-windowshosting) sur le système hôte. Le bundle installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Le module crée le proxy inverse entre IIS et le serveur Kestrel. Remarque : Si le système ne dispose pas d’une connexion Internet, obtenez et installez *[Redistributable Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=53840)*  avant d’installer le bundle d’hébergement .NET Core Windows Server.

2. Redémarrez le système ou exécutez **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes pour prendre en compte une modification de la variable système PATH.

> [!NOTE]
> Si vous utilisez une configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Si vous prévoyez de déployer vos applications avec Web Deploy dans Visual Studio, installez la dernière version de Web Deploy sur le système hôte. Pour installer Web Deploy, vous pouvez utiliser [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenir un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/search/result.aspx?q=webdeploy&form=dlc). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="application-configuration"></a>Configuration d’application

### <a name="enabling-the-iisintegration-components"></a>Activation des composants IISIntegration

Incluez une dépendance envers le package *Microsoft.AspNetCore.Server.IISIntegration* dans les dépendances d’application. Incorporez l’intergiciel (middleware) d’intégration IIS dans l’application en ajoutant la méthode d’extension *.UseIISIntegration()* à *WebHostBuilder()*. Notez que le code qui appelle *.UseIISIntegration()* n’affecte pas la portabilité du code.

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseContentRoot(Directory.GetCurrentDirectory())
    .UseIISIntegration()
    .UseStartup<Startup>()
    .Build();
```

### <a name="setting-iisoptions-for-the-iisintegration-service"></a>Définition d’IISOptions pour le service IISIntegration

Pour configurer les options de service *IISIntegration*, incluez une configuration de service pour *IISOptions* dans *ConfigureServices*.

```csharp
services.Configure<IISOptions>(options => {
  ...
});
```

| Option | Paramètre|
| --- | --- | 
| AutomaticAuthentication | Si la valeur est true, l’intergiciel d’authentification modifie l’utilisateur de la demande qui arrive et répond aux demandes génériques. Si la valeur est false, l’intergiciel d’authentification fournit l’identité et répond aux demandes uniquement quand cela est indiqué explicitement par le schéma d’authentification. |
| ForwardClientCertificate | Si la valeur est true et que l’en-tête de requête `MS-ASPNETCORE-CLIENTCERT` est présent, `ITLSConnectionFeature` est renseigné. |
| ForwardWindowsAuthentication | Si la valeur est true, l’intergiciel d’authentification tente d’effectuer l’authentification à l’aide de l’authentification Windows de gestionnaire de plateforme. Si la valeur est false, l’intergiciel d’authentification n’est pas ajouté. |

### <a name="webconfig"></a>web.config

Le fichier *web.config* configure le Module ASP.NET Core et fournit d’autres configurations IIS. La création, la transformation et la publication de *web.config* sont gérées par `Microsoft.NET.Sdk.Web`, qui est inclus quand vous définissez le SDK de votre projet en haut de votre fichier *.csproj*, `<Project Sdk="Microsoft.NET.Sdk.Web">`. Pour empêcher la cible MSBuild de transformer votre fichier *web.config*, ajoutez la propriété **\<IsTransformWebConfigDisabled>** à votre fichier projet avec le paramètre `true` :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Si vous n’avez pas de fichier *web.config* dans le projet quand vous publiez avec *dotnet publish* ou avec la publication Visual Studio, le fichier est créé pour vous dans la sortie publiée. Si le fichier figure dans votre projet, il est transformé avec les *arguments* et *processPath* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la sortie publiée. La transformation ne touche pas les paramètres de configuration IIS que vous avez inclus dans le fichier.

## <a name="create-the-iis-website"></a>Créer le site web IIS

1. Sur le système IIS cible, créez un dossier pour contenir les dossiers et fichiers publiés de l’application, qui sont décrits dans [Structure de répertoires](xref:hosting/directory-structure).

2. Dans le dossier que vous avez créé, créez un dossier *journaux* pour contenir les journaux d’application (si vous prévoyez d’activer la journalisation). Si vous envisagez de déployer votre application avec un dossier *journaux* dans la charge utile, vous pouvez ignorer cette étape.

3. Dans **IIS Manager**, créez un site web. Spécifiez le **Nom du site** et affectez le dossier de déploiement de l’application que vous avez créé comme **Chemin physique**. Spécifiez la configuration de **Liaison** et créez le site web.

4. Sélectionnez **Aucun code managé** pour le pool d’applications. ASP.NET Core s’exécute dans un processus séparé et gère l’exécution.

5. Ouvrez la fenêtre **Ajouter un site Web**.

   ![Cliquez sur Ajouter un site Web dans le menu contextuel Sites.](iis/_static/add-website-context-menu-ws2016.png)

6. Configurez le site web.

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](iis/_static/add-website-ws2016.png)

7. Dans le panneau **Pools d’applications**, ouvrez la fenêtre **Modifier le pool d’applications** en cliquant sur le pool d’applications du site web et en sélectionnant **Paramètres de base** dans le menu contextuel.

   ![Sélectionnez Paramètres de base dans le menu contextuel du pool d’applications.](iis/_static/apppools-basic-settings-ws2016.png)

8. Affectez la valeur **Aucun code managé** à **Version du CLR .NET**.

   ![Définissez Aucun code managé pour la Version du CLR .NET.](iis/_static/edit-apppool-ws2016.png)
     
    Remarque : L’affectation de la valeur **Aucun code managé** à **Version du CLR .NET** est facultative. ASP.NET Core ne repose pas sur le chargement du CLR de bureau.

9. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

    Si vous remplacez l’identité par défaut du pool d’applications (**Modèle de processus** > **Identité**) **ApplicationPoolIdentity** par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et autres ressources requises.
   
## <a name="deploy-the-application"></a>Déployer l'application
Déployez l’application sur le dossier que vous avez créé sur le système IIS cible. Web Deploy est le mécanisme recommandé pour le déploiement. Les alternatives à Web Deploy sont répertoriées ci-dessous.

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio
Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Créer des profils de publication pour Visual Studio et MSBuild afin de déployer des applications ASP.NET Core](xref:publishing/web-publishing-vs#publish-profiles). Si votre fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio.

![Boîte de dialogue Publier](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio
Vous pouvez également utiliser Web Deploy en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](https://technet.microsoft.com/library/dd568996(WS.10).aspx).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy
Si vous ne souhaitez pas utiliser Web Deploy ou si vous n’utilisez pas Visual Studio, plusieurs méthodes sont à votre disposition pour déplacer l’application vers le système hôte, telles que Xcopy, Robocopy ou PowerShell. Les utilisateurs de Visual Studio peuvent utiliser les [Exemples de publication](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md).

## <a name="browse-the-website"></a>Parcourir le site web
![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](iis/_static/browsewebsite.png)
   
>[!WARNING]
> Les applications .NET Core sont hébergées par le biais d’un proxy inverse entre IIS et le serveur Kestrel. Pour créer le proxy inverse, le fichier *web.config* doit être présent dans le chemin de racine de contenu (généralement le chemin de base de l’application) de l’application déployée, qui est le chemin physique du site web fourni à IIS. Il existe des fichiers sensibles sur le chemin physique de l’application, notamment des sous-dossiers, tels que *my_application.runtimeconfig.json*, *my_application.xml* (commentaires de Documentation XML) et *my_ application.DEPS.JSON*. Le fichier *web.config* est obligatoire pour créer le proxy inverse pour Kestrel, qui empêche qu’IIS traite ces fichiers sensibles et d’autres. **Par conséquent, il est important que le fichier *web.config* ne soit jamais accidentellement renommé ou supprimé du déploiement.**

## <a name="data-protection"></a>Protection des données

Une application ASP.NET Core stocke le porte-clés en mémoire quand les conditions suivantes sont remplies :

* Un site web est hébergé derrière IIS.
* La pile Protection des données n’a pas été configurée pour stocker le porte-clés dans un magasin persistant.

Si le porte-clés est stocké en mémoire, au redémarrage de l’application :

* Tous les jetons d’authentification de formulaires seront non valides. 
* Les utilisateurs devront se reconnecter lors de leur prochaine requête. 
* Toutes les données que vous avez protégées avec le porte-clés ne seront plus sans protection.

> [!WARNING]
> Protection des données est utilisée par plusieurs intergiciels ASP.NET, notamment ceux utilisés lors de l’authentification. Même si vous n’appelez pas spécifiquement des API de Protection des données à partir de votre propre code, vous devez configurer la Protection des données avec un script de déploiement ou dans votre propre code. Si vous ne configurez pas la protection des données, par défaut, les clés sont conservées en mémoire et ignorées au redémarrage de votre application. Le redémarrage invalide tout cookie écrit par l’authentification de cookie, et les utilisateurs doivent se reconnecter.

Pour configurer la Protection des données sous IIS, vous devez adopter l’une des approches suivantes :

* Exécutez un [script powershell](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) pour créer des entrées de Registre appropriées (par exemple, `.\Provision-AutoGenKeys.ps1 DefaultAppPool`). Cela permet de stocker les clés dans le Registre, protégées à l’aide de DPAPI avec une clé à l’échelle de l’ordinateur.
* Configurez le pool d’applications IIS pour charger le profil utilisateur. Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Affectez la valeur `True` à **Charger le profil utilisateur**. Cela permet de stocker les clés sous le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications.
* Ajustez votre code d’application pour [utiliser le système de fichiers en tant que magasin de porte-clés](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le porte-clés et vérifiez qu’il s’agit d’un certificat approuvé. Par exemple, s’il s’agit d’un certificat auto-signé, vous devez le placer dans le magasin racine approuvé.

Quand vous utilisez IIS dans une batterie de serveurs web :

* Utilisez un partage de fichiers accessible par tous les ordinateurs.
* Déployez un certificat X509 sur chaque ordinateur.  Configurez la [protection des données dans le code](https://docs.asp.net/en/latest/security/data-protection/configuration/overview.html).

### <a name="1-create-a-data-protection-registry-hive"></a>1. Créer une ruche de Registre de Protection des données

Les clés de Protection des données utilisées par les applications ASP.NET sont stockées dans des ruches de Registre externes aux applications. Pour conserver les clés pour une application donnée, vous devez créer une ruche de Registre pour le pool d’applications de l’application.

Pour les installations IIS autonomes, vous pouvez utiliser le [script PowerShell Provision-AutoGenKeys.ps1 de Protection des données](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre spéciale dans le Registre HKLM, qui fait l’objet d’une liste de contrôle d’accès uniquement pour le compte du processus de travail. Les clés sont chiffrées au repos à l’aide de DPAPI.

Dans les scénarios de batterie de serveurs web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son porte-clés de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vous devez vérifier que les autorisations de fichiers pour un tel partage sont limitées au compte Windows sous lequel s’exécute l’application. Vous pouvez également choisir de protéger les clés au repos à l’aide d’un certificat X509. Vous souhaiter peut-être mettre en œuvre un mécanisme permettant aux utilisateurs de charger des certificats, de les placer dans le magasin de certificats approuvés de l’utilisateur, et de s’assurer qu’ils sont disponibles sur tous les ordinateurs où s’exécutera l’application de l’utilisateur. Pour plus d’informations, consultez [Configuration de la Protection des données](xref:security/data-protection/configuration/overview#data-protection-configuring).

### <a name="2-configure-the-iis-application-pool-to-load-the-user-profile"></a>2. Configurer le pool d’applications IIS pour charger le profil utilisateur
Ce paramètre se trouve dans la section Modèle de processus sous les Paramètres avancés du pool d’applications. Affectez la valeur True à Charger le profil utilisateur. Cela permet de stocker les clés sous le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications.

### <a name="3-machine-wide-policy-for-data-protection"></a>3. Stratégie au niveau de l’ordinateur pour la protection des données

Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy#data-protection-configuration-machinewidepolicy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d’informations, consultez la documentation de [protection des données](xref:security/data-protection/index).

## <a name="configuration-of-sub-applications"></a>Configuration des sous-applications

Les sous-applications ajoutées sous l’application racine ne doivent pas inclure le Module ASP.NET Core en tant que gestionnaire. Si vous ajoutez le module en tant que gestionnaire dans le fichier *web.config* d’une sous-application, vous recevez une erreur 500.19 (erreur interne du serveur) référençant le fichier de configuration défectueux quand vous essayez d’accéder à la sous-application. L’exemple suivant montre le contenu d’un fichier *web.config* publié pour une sous-application ASP.NET Core :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
        arguments=".\MyApp.dll" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Si vous envisagez d’héberger une sous-application non-ASP.NET Core sous une application ASP.NET Core, vous devez supprimer explicitement le gestionnaire hérité dans le fichier *web.config* de la sous-application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore"/>
    </handlers>
    <aspNetCore processPath="dotnet" 
        arguments=".\MyApp.dll" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Pour plus d’informations sur la configuration du Module ASP.NET Core avec le fichier *web.config*, consultez la rubrique [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) et la [Référence de configuration du Module ASP.NET Core](xref:hosting/aspnet-core-module).

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est toujours influencée par la section `<system.webServer>` de *web.config* pour les fonctionnalités d’IIS qui s’appliquent à une configuration de proxy inverse. Par exemple, vous avez peut-être configuré IIS au niveau du système pour qu’il utilise la compression dynamique, mais vous pouvez désactiver ce paramètre pour une application avec l’élément `<urlCompression>` dans le fichier *web.config* de l’application. Pour plus d’informations, consultez la [référence de configuration pour `<system.webServer>`](https://www.iis.net/configreference/system.webserver), [Référence de configuration du Module ASP.NET Core](xref:hosting/aspnet-core-module) et [Utilisation des modules IIS avec ASP.NET Core](xref:hosting/iis-modules). Si vous devez définir des variables d’environnement pour des applications qui s’exécutent dans des pools d’applications isolés (prise en charge sur IIS 10.0+), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Contrairement aux applications .NET Framework configurées avec les éléments `<system.web>`, `<appSettings>`, `<connectionStrings>` et `<location>` dans *web.config*, les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration).

## <a name="application-pools"></a>Pools d'applications

Quand vous hébergez plusieurs sites web sur un même système, vous devez isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications. La boîte de dialogue **Ajouter un site Web** d’IIS applique ce comportement par défaut. Quand vous fournissez un **Nom du site**, le texte est automatiquement transféré vers la zone de texte **Pool d’applications**. Un nouveau pool d’applications est créé avec le nom du site quand vous ajoutez le site web.

## <a name="application-pool-identity"></a>Identité du pool d’applications

Un compte d’identité du pool d’applications vous permet d’exécuter une application sous un compte unique sans avoir à créer et à gérer des domaines ou des comptes locaux. Sur IIS 8.0+, le processus de travail administration IIS (WAS, Admin Worker Process) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus de travail du pool d’applications sous ce compte par défaut. Dans la Console de gestion IIS, sous Paramètres avancés pour votre pool d’applications, vérifiez que l’identité est configurée pour utiliser **ApplicationPoolIdentity** comme indiqué dans l’image ci-dessous.

![Boîte de dialogue Paramètres avancés du pool applications](iis/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur de sécurité avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la Console de gestion d’utilisateur Windows.

Si vous devez accorder au processus de travail IIS un accès élevé à votre application, vous devez modifier la liste de contrôle d’accès pour le répertoire contenant votre application.

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

2. Cliquez avec le bouton droit sur le répertoire et cliquez sur **Propriétés**.

3. Sous l’onglet **Sécurité**, cliquez sur le bouton **Modifier**, puis sur **Ajouter**.

4. Cliquez sur le bouton **Emplacements** et veillez à sélectionner votre système.

5. Entrez **IIS AppPool\DefaultAppPool** dans la zone de texte **Entrez les noms d’objets à sélectionner**.

  ![Boîte de dialogue de sélection d’utilisateurs ou de groupes pour le dossier d’application](iis/_static/select-users-or-groups-1.png)

6. Cliquez sur le bouton **Vérifier les noms**, puis cliquez sur **OK**.

  ![Boîte de dialogue de sélection d’utilisateurs ou de groupes pour le dossier d’application](iis/_static/select-users-or-groups-2.png)

Vous pouvez également effectuer cette opération par le biais d’une invite de commandes à l’aide de l’outil **ICACLS** :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Pour diagnostiquer les problèmes avec les déploiements IIS, étudiez la sortie du navigateur, examinez le journal **Application** du système par le biais de l’**Observateur d’événements** et activez la journalisation `stdout`. Le journal du **Module ASP.NET Core** est accessible au chemin fourni dans l’attribut *stdoutLogFile* de l’élément `<aspNetCore>` dans *web.config*. Tous les dossiers sur le chemin fourni dans la valeur d’attribut doivent exister dans le déploiement. Vous devez également définir *stdoutLogEnabled="true"*. Les applications qui utilisent le SDK `Microsoft.NET.Sdk.Web` pour créer le fichier *web.config* affectent par défaut la valeur *false* à *stdoutLogEnabled*. Par conséquent, vous devez fournir le fichier *web.config* manuellement ou modifiez le fichier afin d’activer la journalisation `stdout`.

Plusieurs des erreurs courantes n’apparaissent dans le navigateur, dans le journal des applications et dans le journal du Module ASP.NET Core qu’une fois les valeurs *startupTimeLimit* (par défaut : 120 secondes) et *startupRetryCount* (par défaut : 2) dépassées. Vous devez donc patienter six minutes avant d’en déduire que le module n’a pas réussi à démarrer un processus pour l’application.

L’un des moyens les plus rapides pour déterminer si l’application fonctionne correctement consiste à exécuter l’application directement sur Kestrel. Si l’application a été publiée en tant que déploiement dépendant du framework, exécutez **dotnet my_application.dll** dans le dossier de déploiement, qui est le chemin physique IIS vers l’application. Si l’application a été publiée en tant que déploiement autonome, exécutez le fichier exécutable de l’application directement à partir d’une invite de commandes, **my_application.exe**, dans le dossier de déploiement. Si Kestrel est à l’écoute sur le port par défaut 5000, vous devez pouvoir accéder à l’application à l’adresse `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration IIS-Module ASP.NET Core-Kestrel plutôt qu’à l’application proprement dite.

Pour déterminer si le proxy inverse IIS pour le serveur Kestrel fonctionne correctement, vous pouvez envoyer une requête de fichier statique simple pour une feuille de style, un script ou une image à partir des fichiers statiques de l’application dans *wwwroot* à l’aide de [l’intergiciel de fichiers statiques](xref:fundamentals/static-files). Si l’application peut traiter les fichiers statiques mais que les vues MVC et autres points de terminaison échouent, le problème est sans doute lié à l’application proprement dite (par exemple, routage MVC ou Erreur de serveur interne 500) plutôt qu’à la configuration IIS-Module ASP.NET Core-Kestrel.

Quand Kestrel démarre normalement derrière IIS, mais que l’application ne s’exécute pas sur le système après s’être exécutée correctement localement, vous pouvez ajouter temporairement une variable d’environnement *web.config* pour affecter la valeur `Development` à `ASPNETCORE_ENVIRONMENT`. Tant que vous ne substituez pas l’environnement de démarrage de l’application, cela permet à la [page d’exception de développeur](xref:fundamentals/error-handling) de s’afficher quand l’application est exécutée sur le système. La définition de la variable d’environnement `ASPNETCORE_ENVIRONMENT` de cette façon est recommandée uniquement pour les systèmes de préproduction et de test qui ne sont pas exposés à Internet. Veillez à supprimer la variable d’environnement du fichier *web.config* une fois que vous avez terminé. Pour plus d’informations sur la définition des variables d’environnement par le biais de *web.config* pour le proxy inverse, consultez la section sur [l’élément enfant environmentVariables d’aspNetCore](xref:hosting/aspnet-core-module#setting-environment-variables).

Dans la plupart des cas, l’activation de la journalisation de l’application aide à résoudre les problèmes liés à l’application ou au proxy inverse. Pour plus d’informations, consultez [Journalisation](xref:fundamentals/logging).

Notre dernier conseil de dépannage concerne les applications dont l’exécution échoue après la mise à niveau du SDK .NET Core sur l’ordinateur de développement ou des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en supprimant les dossiers `bin` et `obj` du projet, en effaçant les caches de package `%UserProfile%\.nuget\packages\` et `%LocalAppData%\Nuget\v3-cache`, en restaurant le projet et en vérifiant que votre déploiement préalable sur le système a été supprimé complètement avant de redéployer l’application.

>[!TIP]
> Un moyen pratique pour effacer les caches de package consiste à obtenir l’outil `NuGet.exe` à partir de [NuGet.org](https://www.nuget.org/), à l’ajouter à votre variable système PATH et à exécuter `nuget locals all -clear` à partir d’une invite de commandes.

## <a name="common-errors"></a>Erreurs courantes

Cette liste d’erreurs n’est pas exhaustive. Si vous rencontrez une erreur non répertoriée ici, veuillez laisser un message d’erreur détaillé dans la section Commentaires ci-dessous.

### <a name="installer-unable-to-obtain-vc-redistributable"></a>Le programme d’installation ne parvient pas à obtenir VC++ Redistributable

* **Exception de programme d’installation :** 0x80072efd ou 0x80072f76 - Erreur non spécifiée

* **Exception du journal d’installation&#8224;:** Erreur 0x80072efd ou 0x80072f76 : Impossible d’exécuter le package EXE

  &#8224;Le journal se trouve dans C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log.

Résolution des problèmes :

* Si le système n’a pas accès à Internet lors de l’installation du bundle d’hébergement du serveur, cette exception se produit quand le programme d’installation ne peut pas obtenir *Microsoft Visual C++ 2015 Redistributable*. Vous pouvez obtenir un programme d’installation à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53840). Si le programme d’installation échoue, vous risquez de ne pas recevoir le runtime .NET Core nécessaire pour héberger des déploiements dépendants du framework. Si vous envisagez d’héberger des déploiements dépendants du framework, vérifiez que le runtime est installé dans Programmes &amp; Fonctionnalités. Vous pouvez obtenir un programme d’installation du runtime à partir de [Téléchargements .NET](https://www.microsoft.com/net/download/core). Après avoir installé le runtime, redémarrez le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>La mise à niveau du système d’exploitation a supprimé le Module ASP.NET Core 32 bits

* **Journal des applications :** Le fichier DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** du module n’a pas pu se charger. Les données sont erronées.

Résolution des problèmes :

* Les fichiers autres que les fichiers du système d’exploitation dans le répertoire **C:\Windows\SysWOW64\inetsrv** ne sont pas conservés pendant une mise à niveau du système d’exploitation. Si vous avez installé le Module ASP.NET Core avant une mise à niveau du système d’exploitation et que vous essayez ensuite d’exécuter un pool d’applications en mode 32 bits après une mise à niveau du système d’exploitation, ce problème se produit. Après une mise à niveau du système d’exploitation, réparez le Module ASP.NET Core. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](#install-the-net-core-windows-server-hosting-bundle). Sélectionnez **Réparer** quand vous exécutez le programme d’installation.

### <a name="platform-conflicts-with-rid"></a>Conflits de plateforme avec RID

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application « MACHINE/WEBROOT/APPHOST/MY_APPLICATION » avec la racine physique 'C:\{PATH}\' n’a pas pu démarrer le processus avec la ligne de commande '« C:\\{PATH}\my_application.{exe|dll} » ', Code d’erreur = 0x80004005 : ff.

* **Journal du Module ASP.NET Core :** Exception non gérée : System.BadImageFormatException : Impossible de charger le fichier ou l’assembly « my_application.dll ». Tentative de chargement d’un programme au format incorrect.

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [Conseils de dépannage](#troubleshooting-tips).

* Vérifiez que vous n’avez pas défini un `<PlatformTarget>` dans votre *.csproj* qui est en conflit avec le RID. Par exemple, ne spécifiez pas `x86` comme `<PlatformTarget>` et en publiant avec un RID `win10-x64`, à l’aide de *dotnet publish -c Release -r win10-x64* ou en affectant la valeur `win10-x64` à `<RuntimeIdentifiers>` dans votre *.csproj*. Le projet sera publié sans avertissement ni erreur, mais il échouera avec les exceptions ci-dessus sur le système.

* Si cette exception se produit pour un déploiement d’applications Azure lors de la mise à niveau d’une application et du déploiement de nouveaux assemblys, supprimez manuellement tous les fichiers du déploiement précédent. Le fait de laisser des assemblys incompatibles peut provoquer une exception `System.BadImageFormatException` lors du déploiement d’une application mise à niveau.

### <a name="uri-endpoint-wrong-or-stopped-website"></a>Point de terminaison d’URI incorrect ou site web arrêté

* **Navigateur :** ERR_CONNECTION_REFUSED

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Vérifiez que vous utilisez le point de terminaison d’URI correct pour l’application. Vérifiez vos liaisons.

* Vérifiez que le site web IIS n’est pas à l’état *Arrêté*.

### <a name="corewebengine-or-w3svc-server-features-disabled"></a>Fonctionnalités de serveur W3SVC ou CoreWebEngine désactivées

* **Exception de système d’exploitation :** Les fonctionnalités CoreWebEngine et W3SVC d’IIS 7.0 doivent être installées pour utiliser le Module ASP.NET Core.

Résolution des problèmes :

* Vérifiez que vous avez activé le rôle et les fonctionnalités appropriés. Consultez [Configuration d’IIS](#iis-configuration).

### <a name="incorrect-website-physical-path-or-application-missing"></a>Chemin physique de site web incorrect ou application manquante

* **Navigateur :** 403 - Interdit : accès refusé **--ou--** 403.14 - Interdit : Le serveur Web est configuré pour ne pas afficher le contenu de ce répertoire.

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Consultez les **Paramètres de base** du site web IIS et le dossier d’application physique. Vérifiez que l’application est dans le dossier sur le **chemin physique** du site web IIS.

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a>Rôle incorrect, module non installé ou autorisations incorrectes

* **Navigateur :** 500.19 Erreur interne du serveur : Impossible d’accéder à la page que vous avez demandée, car les données de configuration connexes relatives à la page ne sont pas valides.

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Vérifiez que vous avez activé le rôle approprié. Consultez [Configuration d’IIS](#iis-configuration).

* Accédez à **Programmes &amp; Fonctionnalités** et vérifiez que le **Module Microsoft ASP.NET Core** a été installé. Si le **Module Microsoft ASP.NET Core** n’est pas présent dans la liste des programmes installés, installez-le. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](#install-the-net-core-windows-server-hosting-bundle).

* Vérifiez que **Pool d’applications > Modèle de processus > Identité** a la valeur **ApplicationPoolIdentity** ou que votre identité personnalisée dispose des autorisations appropriées pour accéder au dossier de déploiement de l’application.

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorrect, variable de chemin manquante, bundle d’hébergement non installé, système/IIS non redémarré, VC++ Redistributable non installé ou violation d’accès dotnet.exe

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' avec la racine physique 'C:\\{PATH}\' n’a pas pu démarrer le processus avec la ligne de commande' « .\my_application.exe » ', Code d’erreur = '0x80070002 : 0.

* **Journal du Module ASP.NET Core :** Fichier journal créé mais vide

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [Conseils de dépannage](#troubleshooting-tips).

* Vérifiez l’attribut *processPath* de l’élément `<aspNetCore>` dans *web.config* pour confirmer qu’il s’agit de *dotnet* pour un déploiement dépendant du framework ou de *.\my_application.exe* pour un déploiement autonome.

* Pour un déploiement dépendant du framework, *dotnet.exe* n’est peut-être pas accessible par le biais des paramètres PATH. Vérifiez que *C:\Program Files\dotnet\* existe dans les paramètres PATH du système.

* Pour un déploiement dépendant du framework, *dotnet.exe* n’est peut-être pas accessible pour l’identité d’utilisateur du pool d’applications. Vérifiez que l’identité utilisateur du pool d’applications a accès au répertoire *C:\Program Files\dotnet*. Vérifiez qu’aucune règle de refus d’accès n’est configurée pour l’identité utilisateur du pool d’applications sur les répertoires *C:\Program Files\dotnet* et d’application.

* Peut-être avez-vous déployé un déploiement dépendant du framework et installé .NET Core sans redémarrer IIS. Redémarrez le serveur ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Peut-être avez-vous déployé un déploiement dépendant du framework sans avoir installé le runtime .NET Core sur le système hôte. Si vous tentez de déployer un déploiement dépendant du framework et que vous n’avez pas installé le runtime .NET Core, exécutez le **programme d’installation du bundle d’hébergement .NET Core Windows Server** sur le système. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](#install-the-net-core-windows-server-hosting-bundle). Si vous essayez d’installer le runtime .NET Core sur un système sans connexion Internet, obtenir le runtime à partir de [Téléchargements .NET](https://www.microsoft.com/net/download/core) et exécutez le programme d’installation du bundle d’hébergement pour installer le Module ASP.NET Core. Terminez l’installation en redémarrant le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Peut-être avez-vous déployé un déploiement dépendant du framework et installé .NET Core sans redémarrer le système ou IIS. Redémarrez le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Peut-être avez-vous déployé un déploiement dépendant du framework et *Microsoft Visual C++ 2015 Redistributable (x64)* n’est pas installé sur le système. Vous pouvez obtenir un programme d’installation à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53840).

### <a name="incorrect-arguments-of-aspnetcore-element"></a>Arguments incorrects de l’élément \<aspNetCore\>

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' avec la racine physique 'C:\\{PATH}\' n’a pas pu démarrer le processus avec la ligne de commande' « dotnet » .\my_application.dll » ', Code d’erreur = '0x80004005 : 80008081.

* **Journal du Module ASP.NET Core :** L’application à exécuter n’existe pas : 'PATH\my_application.dll'

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [Conseils de dépannage](#troubleshooting-tips).

* Examinez l’attribut *arguments* de l’élément `<aspNetCore>` dans *web.config* pour confirmer (a) qu’il s’agit de *.\my_applciation.dll* pour un déploiement dépendant framework, ou (b) qu’il est absent ou qu’il s’agit d’une chaîne vide (*arguments=""*) ou d’une liste d’arguments de votre application (*arguments="arg1, arg2, ..."*) pour un déploiement autonome.

### <a name="missing-net-framework-version"></a>Version du .NET Framework manquante

* **Navigateur :** 502.3 Passerelle incorrecte : Une erreur de connexion s’est produite lors de la tentative de routage de la requête.

* **Journal des applications :** Code d’erreur = L’application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' avec la racine physique 'C:\\{PATH}\' n’a pas pu démarrer le processus avec la ligne de commande' « dotnet » .\my_application.dll » ', Code d’erreur = '0x80004005 : 80008081.

* **Journal du Module ASP.NET Core :** Exception d’assembly, de méthode ou de fichier manquante. La méthode, le fichier ou l’assembly spécifié dans l’exception est une méthode, un fichier ou un assembly .NET Framework.

Résolution des problèmes :

* Installez la version de .NET Framework manquante sur le système.

* Pour un déploiement dépendant du framework, vérifiez que le runtime approprié est installé sur le système. Pour exemple, si vous mettez à niveau un projet de la version 1.0 vers la version 1.1, que vous déployez sur le système d’hébergement et que vous recevez cette exception, vérifiez que la version 1.1 du framework est installée sur le système hôte.

### <a name="stopped-application-pool"></a>Pool d’applications arrêté

* **Navigateur :** 503 Service non disponible

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes

* Vérifiez que le pool d’applications n’est pas à l’état *Arrêté*.

### <a name="iis-integration-middleware-not-implemented"></a>L’intergiciel d’intégration IIS n’est pas implémenté

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' avec racine physique 'C:\\{PATH}\' a créé des processus avec la ligne de commande '« C:\\{PATH}\my_application.{exe|dll} » ' mais s’est bloquée ou n’a pas répondu ou écouté sur le port spécifié '{PORT}', Code d’erreur = '0x800705b4'

* **Journal du Module ASP.NET Core :** Fichier journal créé et indiquant un fonctionnement normal.

Résolution des problèmes

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [Conseils de dépannage](#troubleshooting-tips).

* Vérifiez que vous avez référencé correctement l’intergiciel d’intégration IIS en appelant la méthode *.UseIISIntegration()* sur le *WebHostBuilder()* de l’application.

### <a name="sub-application-includes-a-handlers-section"></a>La sous-application inclut une section \<handlers\>

* **Navigateur :** Erreur HTTP 500.19 : Erreur interne du serveur

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal créé et indiquant un fonctionnement normal pour l’application racine. Fichier journal non créé pour la sous-application.

Résolution des problèmes

* Vérifiez que le fichier *web.config* de la sous-application n’inclut pas de section `<handlers>`.

### <a name="application-configuration-general-issue"></a>Problème général lié à la configuration d’application

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' avec racine physique 'C:\\{PATH}\' a créé des processus avec la ligne de commande '« C:\\{PATH}\my_application.{exe|dll} » ' mais s’est bloquée ou n’a pas répondu ou écouté sur le port spécifié '{PORT}', Code d’erreur = '0x800705b4'

* **Journal du Module ASP.NET Core :** Fichier journal créé mais vide

Résolution des problèmes

* Cette exception générale indique que le démarrage du processus a échoué, probablement en raison d’un problème de configuration d’application. En vous référant à [Structure de répertoires](xref:hosting/directory-structure), vérifiez que les fichiers et dossiers déployés de votre application sont corrects et que les fichiers de configuration de votre application sont présents et contiennent les paramètres appropriés pour votre application et votre environnement. Pour plus d’informations, consultez [Conseils de dépannage](#troubleshooting-tips).

## <a name="resources"></a>Ressources

* [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)

* [Informations de référence sur la configuration du Module ASP.NET Core](xref:hosting/aspnet-core-module)

* [Utilisation de modules IIS avec ASP.NET Core](xref:hosting/iis-modules)

* [Présentation d’ASP.NET Core](../index.md)

* [Site officiel de Microsoft IIS](http://www.iis.net/)

* [Bibliothèque Microsoft TechNet : Windows Server](https://technet.microsoft.com/library/bb625087.aspx)
