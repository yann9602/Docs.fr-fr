---
title: "Créer des profils de publication pour Visual Studio et MSBuild"
author: rick-anderson
description: "Explique ce qu’est la publication web dans Visual Studio."
keywords: "ASP.NET Core, publication web, publication, msbuild, déploiement web, dotnet publish, Visual Studio 2017"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
ms.openlocfilehash: 872e8c99734a0913770281d9d87b1e30c250ab0a
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="create-publish-profiles-for-visual-studio-and-msbuild-to-deploy-aspnet-core-apps"></a>Créer des profils de publication pour Visual Studio et MSBuild afin de déployer des applications ASP.NET Core

De [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article est axé sur l’utilisation de Visual Studio 2017 pour créer des profils de publication. Les profils de publication créées avec Visual Studio peuvent être exécutés à partir de MSBuild et Visual Studio 2017.

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.1" />
  </ItemGroup>

</Project>
```

---

L’attribut `Sdk` dans l’élément `<Project>` (sur la première ligne) du balisage ci-dessus effectue les opérations suivantes :

* Importation du fichier `props` à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* au début.
* Importation du fichier targets à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* à la fin.

L’emplacement par défaut de `MSBuildSDKsPath` (avec Visual Studio 2017 Enterprise) est le dossier *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` dépend de :

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

Ce qui provoque l’importation des fichiers `props` et `targets` suivants :

* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props
* $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets

Les cibles de publication réimporteront l’ensemble approprié de cibles en fonction de la méthode de publication utilisée.

Quand MSBuild ou Visual Studio charge un projet, les actions principales suivantes sont effectuées :

* Génération du projet
* Calcul des fichiers à publier
* Publication des fichiers sur la destination

### <a name="compute-project-items"></a>Calcul des éléments du projet

Quand le projet est chargé, les éléments du projet (fichiers) sont calculés. L’attribut `item type` détermine comment le fichier est traité. Par défaut, les fichiers *.cs* sont inclus dans la liste d’éléments `Compile`. Les fichiers dans la liste d’éléments `Compile` sont compilés.

La liste d’éléments `Content` contient des fichiers qui seront publiés en plus des sorties de génération. Par défaut, les fichiers qui correspondent au modèle wwwroot/** sont inclus dans l’élément `Content`. [wwwroot/** est un modèle d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns) qui spécifie tous les fichiers dans le dossier *wwwroot* **et** les sous-dossiers. Si vous devez ajouter explicitement un fichier à la liste de publication, vous pouvez l’ajouter directement au fichier *.csproj* comme indiqué dans [Inclusion de fichiers](#including-files).

Quand vous sélectionnez le bouton **Publier** dans Visual Studio ou quand vous publiez à partir de la ligne de commande :

- Les éléments/propriétés sont calculés (il s’agit des fichiers nécessaires à la génération).
- Visual Studio uniquement : les packages NuGet sont restaurés.  (La restauration doit être explicite par l’utilisateur sur l’interface CLI.)
- Le projet est généré.
- Les éléments de publication sont calculés (il s’agit des fichiers nécessaires à la publication).
- Le projet est publié. (Les fichiers calculés sont copiés sur la destination de publication.)

## <a name="simple-command-line-publishing"></a>Publication simple sur une ligne de commande

La procédure décrite dans cette section fonctionne sur toutes les plateformes .NET Core prises en charge et ne nécessite pas Visual Studio. Dans les exemples ci-dessous, la commande `dotnet publish` est exécutée à partir du répertoire du projet (qui contient le fichier *.csproj*). Si vous n’êtes pas dans le dossier du projet, vous pouvez transmettre explicitement le chemin du fichier projet. Exemple :

```console
dotnet publish  c:/webs/web1
```

Exécutez les commandes suivantes pour créer et publier une application web :

```console
dotnet new mvc
dotnet restore
dotnet publish
```

`dotnet publish` produit une sortie semblable à la suivante :

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.1.548.43366
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp1.1\Web1.dll
```

Le dossier de publication par défaut est `bin\$(Configuration)\netcoreapp<version>\publish`. La valeur par défaut de `$(Configuration)` est Debug. Dans l’exemple ci-dessus, le `<TargetFramework>` est `netcoreapp1.1`. Le chemin réel dans l’exemple ci-dessus est *bin\Debug\netcoreapp1.1\publish*.

`dotnet publish -h` affiche des informations d’aide relatives à la publication.

La commande suivante spécifie une build `Release` et le répertoire de publication :

```console
dotnet publish -c Release -o C:/MyWebs/test
```

La commande `dotnet publish` appelle `MSBuild`, qui appelle la cible de `Publish`. Les paramètres passés à `dotnet publish` sont passés à `MSBuild`. Le paramètre `-c` est mappé à la propriété MSBuild `Configuration`. Le paramètre `-o` est mappé à `OutputPath`.

Vous pouvez passer des propriétés MSBuild à l’aide de l’un des formats suivants :

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

La commande suivante publie une build `Release` sur un partage réseau :

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

Le partage réseau est spécifié avec des barres obliques (*//r8/*) et fonctionne sur toutes les plateformes .NET Core prises en charge.

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.

## <a name="publish-profiles"></a>Profils de publication

Cette section utilise Visual Studio 2017 et versions ultérieures pour créer des profils de publication. Une fois les profils créés, vous pouvez publier à partir de Visual Studio ou de la ligne de commande.

Les profils de publication peuvent simplifier le processus de publication. Vous pouvez avoir plusieurs profils de publication. Pour créer un profil de publication dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Publier**. Vous pouvez également sélectionner **Publier \<nom_projet>** dans le menu Générer. L’onglet **Publier** de la page de capacités d’application s’affiche. Si le projet ne contient pas de profil de publication, la page suivante s’affiche :

![L’onglet **Publier** de la page de capacités d’application montrant Azure, IIS, FTB, Dossier avec Azure sélectionné. Contient également les cases d’option Créer nouveau et Sélectionner.](web-publishing-vs/_static/az.png)

Quand **Dossier** est sélectionné, le bouton **Publier** crée un dossier de profil de publication et le publie.

![L’onglet **Publier** de la page de capacités d’application montrant Azure, IIS, FTB, Dossier](web-publishing-vs/_static/pub1.png)

Une fois qu’un profil de publication a été créé, l’onglet **Publier** change et vous sélectionnez **Créer un profil** pour créer un profil.

![L’onglet **Publier** de la page de capacités d’application montrant FolderProfile (créé lors de la dernière étape) et le lien Créer un profil](web-publishing-vs/_static/create_new.png)

L’Assistant Publication prend en charge les cibles de publication suivantes :

- Microsoft Azure App Service
- IIS, FTP, etc. (pour n’importe quel serveur web)
- Dossier
- Importer un profil (vous permet d’importer un profil)
- Machines virtuelles Microsoft Azure

Pour plus d’informations, consultez [Quelles options de publication choisir ?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options).

Quand vous créez un profil de publication avec Visual Studio, un fichier MSBuild *Properties/PublishProfiles/\<nom_publication>.pubxml* est créé. Ce fichier *.pubxml* est un fichier MSBuild qui contient des paramètres de configuration de publication. Vous pouvez modifier ce fichier pour personnaliser le processus de génération et de publication. Ce fichier est lu par le processus de publication. `<LastUsedBuildConfiguration>` est spéciale, car il s’agit d’une propriété globale qui ne doit être dans aucun fichier importé dans la build. Pour plus d’informations, consultez [MSBuild: how to set the configuration property (Comment définir la propriété de configuration)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).
Le fichier *.pubxml* ne doit pas être archivé dans le contrôle de code source, car il dépend du fichier *.user*. Le fichier *.user* ne doit jamais être archivé dans le contrôle de code source, car il peut contenir des informations sensibles et il est valide uniquement pour un utilisateur et un ordinateur.

Les informations sensibles (telles que le mot de passe de publication) sont chiffrées pour chaque utilisateur/ordinateur et stockées dans le fichier *Properties/PublishProfiles/\<nom_publication>.pubxml.user*. Ce fichier pouvant contenir des informations sensibles, il ne doit **pas** être archivé dans le contrôle de code source.

Pour obtenir une vue d’ensemble expliquant comment publier une application web sur ASP.NET Core, consultez [Publication et déploiement](index.md). [Publication et déploiement](index.md) est un projet open source accessible à l’adresse https://github.com/aspnet/websdk.

Actuellement, `dotnet publish` n’offre pas la possibilité d’utiliser des profils de publication. Pour utiliser des profils de publication, utilisez `dotnet build`. `dotnet build` appelle MSBuild sur le projet. Vous pouvez également appeler `msbuild` directement.

Définissez les propriétés MSBuild suivantes lors de l’utilisation d’un profil de publication :

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

Par exemple, lors de la publication avec un profil nommé *FolderProfile* vous pouvez exécuter l’une des commandes ci-dessous.

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Quand vous appelez la commande `dotnet build`, elle appelle `msbuild` pour exécuter le processus de génération et de publication. Appeler `dotnet build` ou `msbuild` revient essentiellement au même quand vous passez un profil de dossier. Quand vous appelez MSBuild directement sur Windows, vous obtenez la version du .NET Framework de MSBuild.  MSDeploy est actuellement limité aux ordinateurs Windows pour la publication. L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild, et MSBuild utilise MSDeploy sur les profils autres que les dossiers de profil. L’appel de `dotnet build` sur un profil autre qu’un profil de dossier entraîne l’appel de MSBuild (à l’aide de MSDeploy) et échoue (même en cas d’exécution sur une plateforme Windows). Pour publier avec un profil autre qu’un profil de dossier, appelez MSBuild directement.

Le profil de publication de dossier suivant a été créé avec Visual Studio et publie sur un partage réseau :

[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]

Notez que `<LastUsedBuildConfiguration>` a la valeur `Release`.  Lors de la publication à partir de Visual Studio, la propriété de configuration `<LastUsedBuildConfiguration>` prend la valeur en vigueur lors du démarrage du processus de publication. La propriété de configuration `<LastUsedBuildConfiguration>` est spéciale et ne doit pas être substituée dans un fichier MSBuild importé. Vous pouvez substituer cette propriété à partir de la ligne de commande. Exemple :

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

À l’aide de MSBuild :

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publier sur un point de terminaison MSDeploy à partir de la ligne de commande

Comme mentionné précédemment, vous pouvez publier à l’aide de la commande `dotnet publish` ou `msbuild`. `dotnet publish` s’exécute dans le contexte de .NET Core. `msbuild` a besoin du .NET framework et est donc limitée aux environnements Windows.

Pour publier avec MSDeploy, le plus simple est d’abord de créer un profil de publication dans Visual Studio 2017, puis d’utiliser le profil à partir de la ligne de commande.

Dans l’exemple suivant, j’ai créé une application web ASP.NET Core (à l’aide de `dotnet new mvc`) et ajouté un profil de publication Azure avec Visual Studio.

Vous exécutez `msbuild` à partir d’une **invite de commandes développeur pour VS 2017**. L’invite de commandes développeur aura le bon *msbuild.exe* dans son chemin et définira certaines variables MSBuild.

MSBuild utilise la syntaxe suivante :

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

Vous pouvez obtenir le `Password` à partir du fichier *\<nom_publication>.PublishSettings*. Vous pouvez télécharger le fichier *.PublishSettings* à partir des emplacements suivants :

- Explorateur de solutions : cliquez avec le bouton droit sur l’application web et sélectionnez **Télécharger le profil de publication**.
- Portail de gestion Azure : sélectionnez **Obtenir le profil de publication** à partir du panneau Application web.

`Username` figure dans le profil de publication.

L’exemple suivant utilise le profil de publication « Web11112 – Web Deploy » :

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a>Exclusion de fichiers

Lors de la publication d’applications web ASP.NET Core, les artefacts de build et le contenu du dossier *wwwroot* sont inclus. `msbuild` prend en charge les [modèles d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns). Par exemple, le balisage d’éléments `<Content>` suivant exclut tous les fichiers texte (*.txt*) du dossier *wwwroot/content* et tous ses sous-dossiers.

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
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` ne supprime pas les cibles *skip* du site de déploiement. Les dossiers et fichiers ciblés par `<Content>` sont supprimés du site de déploiement. Par exemple, supposez que vous avez déployé une application web avec les fichiers suivants :

- *Views/Home/About1.cshtml*
- *Views/Home/About2.cshtml*
- *Views/Home/About3.cshtml*

Si vous avez ajouté le balisage `<MsDeploySkipRules>` suivant, ces fichiers ne sont pas supprimés sur le site de déploiement.

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

Le balisage `<MsDeploySkipRules>` ci-dessus empêche le déploiement des fichiers *ignorés*, mais ne supprime pas ces fichiers une fois qu’ils sont déployés.

Le balisage `<Content>` suivant supprime les fichiers ciblés sur le site de déploiement :

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

L’utilisation du déploiement à partir de la ligne de commande avec le balisage `<Content>` ci-dessus génère une sortie semblable à la suivante :

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

Vous pouvez utiliser le balisage suivant pour inclure un dossier *images* situé en dehors du répertoire de projet dans le dossier *wwwroot/images* du site de publication.

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

Vous pouvez ajouter le balisage au fichier *.csproj* ou au profil de publication. Si vous l’ajoutez au fichier *csproj*, il est inclus dans chaque profil de publication du projet.

Le balisage mis en surbrillance ci-dessous montre comment :

* Copier un fichier situé en dehors du projet dans le dossier *wwwroot*
* Exclure le dossier *wwwroot\Content*
* Exclure *Views\Home\About2.cshtml*

[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]

Pour obtenir d’autres exemples de déploiement, consultez le fichier [Lisez-moi webSDK](https://github.com/aspnet/websdk).

### <a name="run-a-target-before-or-after-publishing"></a>Exécuter une cible avant ou après la publication

Vous pouvez utiliser les cibles intégrées `BeforePublish` et `AfterPublish` pour exécuter une cible avant ou après la cible de publication. Vous pouvez ajouter le balisage suivant au profil de publication pour enregistrer les messages dans la sortie de console avant et après la publication :

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a>Le service Kudu

Pour afficher les fichiers sur votre application web Azure, utilisez le [service Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Ajoutez le jeton `scm` au nom de votre application web. Exemple :

| URL               | Résultat|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | Application web |
| `http://mysite.scm.azurewebsites.net/` | Service Kudu |

Sélectionnez l’élément de menu [Console de débogage](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour afficher/modifier/supprimer/ajouter des fichiers.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (msdeploy) simplifie le déploiement des applications web et des sites web sur les serveurs IIS.

- [https://github.com/ASPNET/websdk](https://github.com/aspnet/websdk/issues) : soumettez vos problèmes et demandez des fonctionnalités pour le déploiement.
