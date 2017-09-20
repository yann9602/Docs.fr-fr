---
title: ASP.NET Core sur Nano Server
author: shirhatti
description: "Découvrez comment prendre une application ASP.NET Core existante et la déployer sur une instance de Nano Server exécutant IIS."
keywords: ASP.NET Core, nano server
ms.author: riande
manager: wpickett
ms.date: 11/04/2016
ms.topic: article
ms.assetid: 50922cf1-ca58-4006-9236-99b7ff2dd0cf
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/nano-server
ms.openlocfilehash: dd1f2c8de58ea8d3a57e64ecc519184400cb52c8
ms.sourcegitcommit: ad01283f299d346cf757c4f4744c48634dc27e73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2017
---
# <a name="aspnet-core-with-iis-on-nano-server"></a>ASP.NET Core avec IIS sur Nano Server

De [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Dans ce didacticiel, vous allez prendre une application ASP.NET Core existante et la déployer sur une instance de Nano Server exécutant IIS.

## <a name="introduction"></a>Introduction

Nano Server est une option d’installation de Windows Server 2016 qui présente un encombrement réduit et offre une meilleure sécurité et une maintenance simplifiée par rapport à Server Core ou à la version Server complète. Pour plus d’informations et pour obtenir des liens de téléchargement pour les versions d’évaluation de 180 jours, consultez la [documentation officielle de Nano Server](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server). 

Il existe trois manières très simples de tester Nano Server. Quand vous vous connectez avec votre compte MS :

1. Vous pouvez télécharger le fichier ISO de Windows Server 2016 et créer une image Nano Server.

2. Téléchargez le disque dur virtuel Nano Server.

3. Créez une machine virtuelle dans Azure à l’aide de l’image Nano Server de la galerie Azure. Si vous n’avez pas de compte Azure, vous pouvez obtenir une version d’évaluation gratuite de 30 jours.

Dans ce didacticiel, nous utiliserons la deuxième option, le disque dur virtuel Nano Server prégénéré de Windows Server 2016.

Avant de poursuivre ce didacticiel, vous aurez besoin de la [sortie publiée](xref:hosting/directory-structure) d’une application ASP.NET Core existante. Vérifiez que votre application est générée pour s’exécuter dans un processus **64 bits**.

## <a name="setting-up-the-nano-server-instance"></a>Configuration de l’instance de Nano Server

[Créez une machine virtuelle à l’aide de Hyper-V](https://technet.microsoft.com/library/hh846766.aspx) sur votre ordinateur de développement à l’aide du disque dur virtuel précédemment téléchargé. L’ordinateur vous invitera à définir un mot de passe avant de vous connecter. Sur la console de la machine virtuelle, appuyez sur F11 pour définir le mot de passe avant la première connexion. Vous devez également vérifier l’adresse IP de votre nouvelle machine virtuelle, soit en vérifiant l’adresse IP fixe de votre serveur DHCP fournie lors de l’approvisionnement de votre machine virtuelle, soit en consultant les paramètres de mise en réseau de la console de récupération Nano Server.

> [!NOTE]
> Supposez que votre nouvelle machine virtuelle s’exécute avec l’adresse IP V4 locale 192.168.1.10.

Vous pouvez maintenant la gérer à l’aide de l’accès distant PowerShell, qui est l’unique façon d’administrer intégralement votre Nano Server.

## <a name="connecting-to-your-nano-server-instance-using-powershell-remoting"></a>Connexion à votre instance de Nano Server à l’aide de l’accès distant PowerShell

Ouvrez une fenêtre PowerShell avec élévation de privilèges pour ajouter votre instance de Nano Server distante à votre liste `TrustedHosts`.

```PowerShell
$nanoServerIpAddress = "192.168.1.10"
Set-Item WSMan:\localhost\Client\TrustedHosts "$nanoServerIpAddress" -Concatenate -Force
```

> [!NOTE]
> Remplacez la variable `$nanoServerIpAddress` par l’adresse IP correcte.

Une fois que vous avez ajouté votre instance de Nano Server à votre `TrustedHosts`, vous pouvez vous y connecter à l’aide de l’accès distant PowerShell.

```PowerShell
$nanoServerSession = New-PSSession -ComputerName $nanoServerIpAddress -Credential ~\Administrator
Enter-PSSession $nanoServerSession
```

Une connexion réussie génère une invite de commandes avec un format ressemblant à ceci : `[192.168.1.10]: PS C:\Users\Administrator\Documents>`

## <a name="creating-a-file-share"></a>Création d’un partage de fichiers

Créez un partage de fichiers sur le serveur Nano afin que l’application publiée puisse y être copiée. Exécutez les commandes suivantes dans la session distante :

```PowerShell
New-Item C:\PublishedApps\AspNetCoreSampleForNano -type directory
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=yes
net share AspNetCoreSampleForNano=c:\PublishedApps\AspNetCoreSampleForNano /GRANT:EVERYONE`,FULL
```

Après avoir exécuté les commandes ci-dessus, vous devez pouvoir accéder à ce partage en visitant `\\192.168.1.10\AspNetCoreSampleForNano` dans l’Explorateur Windows de l’ordinateur hôte.

## <a name="open-port-in-the-firewall"></a>Ouvrir le port dans le pare-feu

Exécutez les commandes suivantes dans la session distante pour ouvrir un port dans le pare-feu afin de permettre à IIS d’écouter le trafic TCP sur le port TCP/8000.

```PowerShell
New-NetFirewallRule -Name "AspNet5 IIS" -DisplayName "Allow HTTP on TCP/8000" -Protocol TCP -LocalPort 8000 -Action Allow -Enabled True
```

## <a name="installing-iis"></a>Installation d’IIS

Ajoutez le fournisseur `NanoServerPackage` à partir de PowerShell Gallery. Une fois le fournisseur installé et importé, vous pouvez installer les packages Windows.

Exécutez les commandes suivantes dans la session PowerShell créée précédemment :

```PowerShell
Install-PackageProvider NanoServerPackage
Import-PackageProvider NanoServerPackage
Install-NanoServerPackage -Name Microsoft-NanoServer-Storage-Package
Install-NanoServerPackage -Name Microsoft-NanoServer-IIS-Package
```

Pour vérifier rapidement si IIS est configuré correctement, accédez à l’URL `http://192.168.1.10/`. Vous devez voir une page d’accueil. Quand IIS est installé, un site web nommé `Default Web Site` à l’écoute sur le port 80 est créé par défaut.

## <a name="installing-the-aspnet-core-module-ancm"></a>Installation du module ASP.NET Core (ANCM)

Le module ASP.NET Core est un module IIS 7.5+ responsable de la gestion des processus des écouteurs HTTP ASP.NET Core et du transfert des requêtes aux processus qu’il gère. À l’heure actuelle, le processus pour installer le module ASP.NET Core pour IIS est manuel. Vous devez installer le [bundle d’hébergement .NET Core Windows Server](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe) sur un ordinateur standard (autre que Nano). Après avoir installé le bundle sur un ordinateur standard, vous devez copier les fichiers suivants sur le partage de fichiers créé précédemment.

Sur un serveur standard (autre que Nano) avec les services IIS, exécutez les commandes de copie suivantes :

```PowerShell
Copy-Item -Path  C:\windows\system32\inetsrv\aspnetcore.dll -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
Copy-Item -Path  C:\windows\system32\inetsrv\config\schema\aspnetcore_schema.xml -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
```

Remplacez `C:\windows\system32\inetsrv` par `C:\Program Files\IIS Express` sur un ordinateur Windows 10.

Du côté Nano, vous devez copier les fichiers suivants à partir du partage de fichiers créé précédemment vers les emplacements valides. Exécutez les commandes de copie suivantes :

```PowerShell
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore.dll -Destination C:\windows\system32\inetsrv\
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore_schema.xml -Destination C:\windows\system32\inetsrv\config\schema\
```

Exécutez le script suivant dans la session distante :

```PowerShell
# Backup existing applicationHost.config
Copy-Item -Path C:\Windows\System32\inetsrv\config\applicationHost.config -Destination  C:\Windows\System32\inetsrv\config\applicationHost_BeforeInstallingANCM.config

Import-Module IISAdministration

# Initialize variables
$aspNetCoreHandlerFilePath="C:\windows\system32\inetsrv\aspnetcore.dll"
Reset-IISServerManager -confirm:$false
$sm = Get-IISServerManager

# Add AppSettings section 
$sm.GetApplicationHostConfiguration().RootSectionGroup.Sections.Add("appSettings")

# Set Allow for handlers section
$appHostconfig = $sm.GetApplicationHostConfiguration()
$section = $appHostconfig.GetSection("system.webServer/handlers")
$section.OverrideMode="Allow"

# Add aspNetCore section to system.webServer
$sectionaspNetCore = $appHostConfig.RootSectionGroup.SectionGroups["system.webServer"].Sections.Add("aspNetCore")
$sectionaspNetCore.OverrideModeDefault = "Allow"
$sm.CommitChanges()

# Configure globalModule
Reset-IISServerManager -confirm:$false
$globalModules = Get-IISConfigSection "system.webServer/globalModules" | Get-IISConfigCollection
New-IISConfigCollectionElement $globalModules -ConfigAttribute @{"name"="AspNetCoreModule";"image"=$aspNetCoreHandlerFilePath}

# Configure module
$modules = Get-IISConfigSection "system.webServer/modules" | Get-IISConfigCollection
New-IISConfigCollectionElement $modules -ConfigAttribute @{"name"="AspNetCoreModule"}
```

> [!NOTE]
> Supprimez les fichiers *aspnetcore.dll* et *aspnetcore_schema.xml* du partage après l’étape ci-dessus.

## <a name="installing-net-core-framework"></a>Installation de .NET Core Framework

Si votre application est publiée comme [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), .NET Core doit être installé sur le serveur. Utilisez le [script PowerShell dotnet-install.ps1](https://dot.net/v1/dotnet-install.ps1) dans une session PowerShell distante pour installer .NET Core sur votre Nano Server. Passez la version CLI avec le commutateur `-Version` :

```console
dotnet-install.ps1 -Version 2.0.0
```

## <a name="publishing-the-application"></a>Publication de l’application

Copiez la sortie publiée de votre application existante à la racine du partage de fichiers.

Vous devrez peut-être modifier votre fichier *web.config* pour qu’il pointe vers l’endroit où vous avez extrait *dotnet.exe*. Vous pouvez également ajouter *dotnet.exe* à votre variable PATH.

Exemple de fichier *web.config* quand *dotnet.exe* n’est **pas** dans la variable PATH :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="C:\dotnet\dotnet.exe" arguments=".\AspNetCoreSampleForNano.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\aspnetcore-stdout" forwardWindowsAuthToken="true" />
  </system.webServer>
</configuration>
```

Exécutez les commandes suivantes dans la session distante pour créer un site dans IIS pour l’application publiée sur un port autre que celui du site web par défaut. Vous devez également ouvrir ce port pour accéder au web. Ce script utilise le `DefaultAppPool` par souci de simplicité. Pour plus d’informations sur l’exécution sous un pool d’applications, consultez [Pools d’applications](xref:publishing/iis#application-pools).

```PowerShell
Import-module IISAdministration
New-IISSite -Name "AspNetCore" -PhysicalPath c:\PublishedApps\AspNetCoreSampleForNano -BindingInformation "*:8000:"
```

## <a name="known-issue-running-net-core-cli-on-nano-server-and-workaround"></a>Problème connu lors de l’exécution de l’interface de ligne de commande .NET Core sur Nano Server et solution de contournement
```PowerShell
New-NetFirewallRule -Name "AspNetCore Port 81 IIS" -DisplayName "Allow HTTP on TCP/81" -Protocol TCP -LocalPort 81 -Action Allow -Enabled True
```

## <a name="running-the-application"></a>Exécution de l’application

L’application web publiée est accessible dans un navigateur à l’adresse `http://192.168.1.10:8000`. Si vous avez configuré la journalisation comme décrit dans [Création et redirection de journal](xref:hosting/aspnet-core-module#log-creation-and-redirection), vous pouvez afficher vos journaux à *C:\PublishedApps\AspNetCoreSampleForNano\logs*.
