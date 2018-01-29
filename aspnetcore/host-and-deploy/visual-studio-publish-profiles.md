---
title: "Profils pour le déploiement d’applications ASP.NET Core de publication de Visual Studio"
author: rick-anderson
description: "Découvrez comment créer des profils de publication pour les applications ASP.NET Core dans Visual Studio."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 09/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 1f403447c39db4ebfe3dafda591602f0dc9db8c3
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a>Profils pour le déploiement d’applications ASP.NET Core de publication de Visual Studio

De [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article est axé sur l’utilisation de Visual Studio 2017 pour créer des profils de publication. Les profils de publication créées avec Visual Studio peuvent être exécutés à partir de MSBuild et Visual Studio 2017. L’article fournit des détails sur le processus de publication. Consultez [Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) pour obtenir des instructions de publication sur Azure.

Le fichier *.csproj* suivant a été créé avec la commande `dotnet new mvc` :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
  </ItemGroup>

</Project>
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.5" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.6" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.3" />
  </ItemGroup>

</Project>
```

---

L’attribut `Sdk` dans l’élément `<Project>` (sur la première ligne) du balisage ci-dessus effectue les opérations suivantes :

* Importe le fichier de propriétés de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* au début.
* Importation du fichier targets à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* à la fin.

L’emplacement par défaut de `MSBuildSDKsPath` (avec Visual Studio 2017 Enterprise) est le dossier *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` dépend de :

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

Ce qui entraîne les propriétés et les cibles à importer suivants :

* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets

Publier l’importation des cibles la droite du jeu de cibles en fonction de la méthode de publication utilisée.

Quand MSBuild ou Visual Studio charge un projet, les actions principales suivantes sont effectuées :

* Génération du projet
* Calcul des fichiers à publier
* Publication des fichiers sur la destination

### <a name="compute-project-items"></a>Calcul des éléments du projet

Quand le projet est chargé, les éléments du projet (fichiers) sont calculés. L’attribut `item type` détermine comment le fichier est traité. Par défaut, les fichiers *.cs* sont inclus dans la liste d’éléments `Compile`. Les fichiers dans la liste d’éléments `Compile` sont compilés.

Le `Content` liste d’éléments contient des fichiers qui sont publiés en plus les sorties de génération. Par défaut, les fichiers correspondant au modèle `wwwroot/**` sont inclus dans le `Content` élément. [wwwroot /\* \* est un modèle de la globalisation](https://gruntjs.com/configuring-tasks#globbing-patterns) qui spécifie tous les fichiers dans le *wwwroot* dossier **et** sous-dossiers. Pour ajouter explicitement un fichier à la liste de publication, ajoutez-le directement au fichier *.csproj* comme indiqué dans [Inclusion de fichiers](#including-files).

Lorsque vous sélectionnez le **publier** bouton dans Visual Studio, ou lors de la publication à partir de la ligne de commande :

* Les éléments/propriétés sont calculés (il s’agit des fichiers nécessaires à la génération).
* Visual Studio uniquement : les packages NuGet sont restaurés. (La restauration doit être explicite par l’utilisateur sur l’interface CLI.)
* Le projet est généré.
* Les éléments de publication sont calculés (il s’agit des fichiers nécessaires à la publication).
* Le projet est publié. (Les fichiers calculés sont copiés sur la destination de publication.)

Lorsque fait référence à un projet ASP.NET Core `Microsoft.NET.Sdk.Web` dans le fichier projet, un *app_offline.htm* fichier est placé à la racine du répertoire d’application web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#appofflinehtm).

## <a name="basic-command-line-publishing"></a>Publication en ligne de commande de base

Publication en ligne de commande fonctionne sur toutes les plateformes prises en charge de .NET Core et ne nécessite pas Visual Studio. Dans les exemples ci-dessous, la commande `dotnet publish` est exécutée à partir du répertoire du projet (qui contient le fichier *.csproj*). Si la valeur n’est pas dans le dossier du projet, passer explicitement dans le chemin d’accès du fichier projet. Exemple :

```console
dotnet publish c:/webs/web1
```

Exécutez les commandes suivantes pour créer et publier une application web :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```console
dotnet new mvc
dotnet publish
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```console
dotnet new mvc
dotnet restore
dotnet publish
```

---

`dotnet publish` produit une sortie semblable à la suivante :

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

Le dossier de publication par défaut est `bin\$(Configuration)\netcoreapp<version>\publish`. La valeur par défaut de `$(Configuration)` est Debug. Dans l’exemple ci-dessus, le `<TargetFramework>` est `netcoreapp2.0`.

`dotnet publish -h` affiche des informations d’aide relatives à la publication.

La commande suivante spécifie une build `Release` et le répertoire de publication :

```console
dotnet publish -c Release -o C:/MyWebs/test
```

Le `dotnet publish` commande appelle MSBuild qui appelle le `Publish` cible. Les paramètres passés à `dotnet publish` sont passés à MSBuild. Le paramètre `-c` est mappé à la propriété MSBuild `Configuration`. Le paramètre `-o` est mappé à `OutputPath`.

Propriétés MSBuild peuvent être passées à l’aide d’un des formats suivants :

* ` p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

La commande suivante publie une build `Release` sur un partage réseau :

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

Le partage réseau est spécifié avec des barres obliques (*//r8/*) et fonctionne sur toutes les plateformes .NET Core prises en charge.

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.

## <a name="publish-profiles"></a>Profils de publication

Cette section utilise Visual Studio 2017 et versions ultérieures pour créer des profils de publication. Une fois créé, la publication à partir de Visual Studio ou de la ligne de commande est disponible.

Les profils de publication peuvent simplifier le processus de publication. Plusieurs profils de publication peut exister. Pour créer un profil de publication dans Visual Studio, avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **publier**. Vous pouvez également sélectionner **publier \<nom du projet >** dans le menu Générer. L’onglet **Publier** de la page de capacités d’application s’affiche. Si le projet ne contient pas de profil de publication, la page suivante s’affiche :

![L’onglet de la publication de la page de capacités application affichant Azure, IIS, FTB, dossier avec Azure sélectionné. Contient également les cases d’option Créer nouveau et Sélectionner.](visual-studio-publish-profiles/_static/az.png)

Quand **Dossier** est sélectionné, le bouton **Publier** crée un dossier de profil de publication et le publie.

![L’onglet **Publier** de la page de capacités d’application montrant Azure, IIS, FTB, Dossier](visual-studio-publish-profiles/_static/pub1.png)

Une fois qu’un profil de publication est créé, le **publier** onglet modifications, puis sélectionnez **créer nouveau profil** pour créer un nouveau profil.

![L’onglet **Publier** de la page de capacités d’application montrant FolderProfile (créé lors de la dernière étape) et le lien Créer un profil](visual-studio-publish-profiles/_static/create_new.png)

L’Assistant Publication prend en charge les cibles de publication suivantes :

* Microsoft Azure App Service
* IIS, FTP, etc. (pour n’importe quel serveur web)
* Dossier
* Importer un profil (permet l’importation du profil).
* Machines virtuelles Microsoft Azure

Pour plus d’informations, consultez [Quelles options de publication choisir ?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options).

Lors de la création d’un profil de publication avec Visual Studio, un *propriétés/PublishProfiles/\<nom de publication > .pubxml* fichier MSBuild est créé. Ce fichier *.pubxml* est un fichier MSBuild qui contient des paramètres de configuration de publication. Ce fichier peut être modifié pour personnaliser la génération et le processus de publication. Ce fichier est lu par le processus de publication. `<LastUsedBuildConfiguration>`est spécial, car elle est une propriété globale et ne doivent pas être dans n’importe quel fichier qui est importé dans la build. Pour plus d’informations, consultez [MSBuild: how to set the configuration property (Comment définir la propriété de configuration)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).
Le *.pubxml* fichier ne doit pas être archivé dans le contrôle de code source, car elle dépend du *.user* fichier. Le fichier *.user* ne doit jamais être archivé dans le contrôle de code source, car il peut contenir des informations sensibles et il est valide uniquement pour un utilisateur et un ordinateur.

Les informations sensibles (telles que le mot de passe de publication) sont chiffrées pour chaque utilisateur/ordinateur et stockées dans le fichier *Properties/PublishProfiles/\<nom_publication>.pubxml.user*. Ce fichier pouvant contenir des informations sensibles, il ne doit **pas** être archivé dans le contrôle de code source.

Pour une vue d’ensemble de la publication d’une application web sur ASP.NET Core, consultez [hôte et déployer](index.md). [Héberger et déployer](index.md) est un projet open source à https://github.com/aspnet/websdk.

`dotnet publish`permet de dossier, MSDeploy, et [KUDU](https://github.com/projectkudu/kudu/wiki) des profils de publication :
 
Dossier (fonctionne sur plusieurs plateformes) :`dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>`

MSDeploy (actuellement ce fonctionne uniquement dans windows depuis MSDeploy n’est pas inter-plateformes) :`dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>`

Package MSDeploy (actuellement ce fonctionne uniquement dans windows depuis MSDeploy n’est pas inter-plateformes) :`dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>`

Dans les exemples précédents, **ne** passer `deployonbuild` à `dotnet publish`.

Pour plus d’informations, consultez [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

`dotnet publish` prend en charge les API KUDU pour publier sur Azure à partir de n’importe quelle plateforme. Prend en charge les API KUDU, mais il est pris en charge par websdk pour plat croisée publier sur Azure de publication de Visual Studio.

Ajoutez un profil de publication au dossier *Properties/PublishProfiles* avec le contenu suivant :

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

Exécutant la commande suivante compresse le contenu de la publication et le publier sur Azure à l’aide de l’API KUDU :

`dotnet publish /p:PublishProfile=Azure /p:Configuration=Release`

Définissez les propriétés MSBuild suivantes lors de l’utilisation d’un profil de publication :

* `DeployOnBuild=true`
* `PublishProfile=<Publish profile name>`

Lors de la publication avec un profil nommé *FolderProfile*, une des commandes ci-dessous peuvent être exécutée :

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Lors de l’appel `dotnet build`, elle appelle `msbuild` pour exécuter la build et de processus de publication. Appel de `dotnet build` ou `msbuild` est fondamentalement équivalent lors du passage d’un profil de dossier. Lorsque vous appelez MSBuild directement sur Windows, la version de .NET Framework de MSBuild est utilisée. MSDeploy est actuellement limité aux ordinateurs Windows pour la publication. L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild, et MSBuild utilise MSDeploy sur les profils autres que les dossiers de profil. L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild (à l’aide de MSDeploy) et échoue (même en cas d’exécution sur une plateforme Windows). Pour publier avec un profil autre qu’un profil de dossier, appelez MSBuild directement.

Le profil de publication de dossier suivant a été créé avec Visual Studio et publie sur un partage réseau :

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Notez que `<LastUsedBuildConfiguration>` a la valeur `Release`. Lors de la publication à partir de Visual Studio, la propriété de configuration `<LastUsedBuildConfiguration>` prend la valeur en vigueur lors du démarrage du processus de publication. Le `<LastUsedBuildConfiguration>` propriété de configuration est spéciale et ne doit pas être substituée dans un fichier MSBuild importé. Cette propriété peut être substituée à partir de la ligne de commande. Exemple :

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

À l’aide de MSBuild :

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publier sur un point de terminaison MSDeploy à partir de la ligne de commande

Comme mentionné précédemment, la publication peut être effectuée à l’aide de `dotnet publish` ou `msbuild` commande. `dotnet publish` s’exécute dans le contexte de .NET Core. Le `msbuild` commande nécessite .NET framework et est donc limitée aux environnements Windows.

Pour publier avec MSDeploy, le plus simple est d’abord de créer un profil de publication dans Visual Studio 2017, puis d’utiliser le profil à partir de la ligne de commande.

Dans l’exemple suivant, une application de web ASP.NET Core est créée (à l’aide de `dotnet new mvc`), et un profil de publication Azure est ajouté avec Visual Studio.

Exécutez `msbuild` d’un **invite de commandes développeur pour VS 2017**. L’invite de commandes développeur possède la bonne *msbuild.exe* dans son chemin d’accès avec un ensemble de variables MSBuild.

MSBuild utilise la syntaxe suivante :

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile> /p:Username=<USERNAME> /p:Password=<PASSWORD>`

Obtenir le `Password` à partir de la  *\<nom de publication >. Paramètres de publication* fichier. Téléchargez le *. Paramètres de publication* à partir d’un fichier de :

* L’Explorateur de solutions : Avec le bouton droit sur l’application Web et sélectionnez **télécharger le profil de publication**.
* Le portail de gestion Azure : Sélectionnez **obtenir le profil de publication** à partir du Panneau de l’application Web.

`Username` figure dans le profil de publication.

L’exemple suivant utilise le profil de publication « Web11112 – Web Deploy » :

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a>Exclusion de fichiers

Lors de la publication d’applications web ASP.NET Core, les artefacts de build et le contenu du dossier *wwwroot* sont inclus. `msbuild` prend en charge les [modèles d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns). Par exemple, `<Content>` balisage d’élément exclut tout le texte (*.txt*) fichiers à partir de la *wwwroot/contenu* dossier et tous ses sous-dossiers.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Vous pouvez ajouter le balisage ci-dessus à un profil de publication ou au fichier *.csproj*. En cas d’ajout au fichier *.csproj*, la règle est ajoutée à tous les profils de publication dans le projet.

Le balisage d’éléments `<MsDeploySkipRules>` suivant exclut tous les fichiers du dossier *wwwroot/content* :

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`ne supprime pas le *ignorer* cibles à partir du site de déploiement. `<Content>`dossiers et fichiers ciblés sont supprimés à partir du site de déploiement. Par exemple, qu'une application web déployée avait les fichiers suivants :

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Si les éléments suivants `<MsDeploySkipRules>` balisage est ajouté, ces fichiers ne pourraient être supprimées sur le site de déploiement.

``` xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Le `<MsDeploySkipRules>` balisage ci-dessus empêche le *ignorée* fichiers ne soient pas depoyed mais ne supprime pas ces fichiers une fois qu’elles sont déployées.

Les éléments suivants `<Content>` balisage supprime les fichiers ciblés sur le site de déploiement :

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

À l’aide d’un déploiement de ligne de commande avec le `<Content>` balisage au-dessus des résultats de sortie similaire à ce qui suit :

``` console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="including-files"></a>Inclusion de fichiers

Le balisage suivant inclut une *images* dossier en dehors du répertoire de projet pour le *wwwroot/images* dossier du site de publication :

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

Vous pouvez ajouter le balisage au fichier *.csproj* ou au profil de publication. S’il est ajouté à la *.csproj* fichier, il est inclus dans chaque profil de publication dans le projet.

Le balisage mis en surbrillance ci-dessous montre comment :

* Copier un fichier situé en dehors du projet dans le dossier *wwwroot*
* Exclure le dossier *wwwroot\Content*
* Exclure *Views\Home\About2.cshtml*

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

Pour obtenir d’autres exemples de déploiement, consultez le fichier [Lisez-moi webSDK](https://github.com/aspnet/websdk).

### <a name="run-a-target-before-or-after-publishing"></a>Exécuter une cible avant ou après la publication

La fonction intégrée `BeforePublish` et `AfterPublish` cibles peuvent servir à exécuter une cible avant ou après la cible de publication. Vous pouvez ajouter le balisage suivant au profil de publication pour enregistrer les messages dans la sortie de console avant et après la publication :

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a>Le service Kudu

Pour afficher les fichiers dans l’un déploiement d’application web Service d’applications Azure, utilisez le [service de Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Ajouter le `scm` le jeton dans le nom de l’application web. Exemple :

| URL                                    | Résultat      |
| -------------------------------------- | ----------- |
| `http://mysite.azurewebsites.net/`     | Application web     |
| `http://mysite.scm.azurewebsites.net/` | Service Kudu |

Sélectionnez l’élément de menu [Console de débogage](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour afficher/modifier/supprimer/ajouter des fichiers.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifie le déploiement des applications web et sites Web aux serveurs IIS.
* [https://github.com/ASPNET/websdk](https://github.com/aspnet/websdk/issues) : soumettez vos problèmes et demandez des fonctionnalités pour le déploiement.
