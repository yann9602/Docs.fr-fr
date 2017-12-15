---
title: "Ajouter des fonctionnalités de l’application à partir d’un assembly externe à l’aide de IHostingStartup dans ASP.NET Core"
author: guardrex
description: "Découvrez comment ajouter des fonctionnalités à une application ASP.NET Core à partir d’un assembly externe à l’aide d’une implémentation IHostingStartup."
ms.author: riande
manager: wpickett
ms.date: 12/07/2017
ms.topic: article
ms.custom: mvc
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/ihostingstartup
ms.openlocfilehash: 971e2d490f65a91c12fc34fa5604557759621467
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="add-app-features-from-an-external-assembly-using-ihostingstartup-in-aspnet-core"></a>Ajouter des fonctionnalités de l’application à partir d’un assembly externe à l’aide de IHostingStartup dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Un [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) implémentation permet d’ajouter des fonctionnalités à une application au démarrage à partir de l’application en dehors de `Startup` classe. Par exemple, une bibliothèque d’outils externes permettre utiliser un `IHostingStartup` l’implémentation pour fournir des services à une application ou fournisseurs de configuration supplémentaires. `IHostingStartup`*est disponible dans ASP.NET Core 2.0 et versions ultérieures.*

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/ihostingstartup/sample/) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="discover-loaded-hosting-startup-assemblies"></a>Détecter les assemblys de démarrage hébergement chargés

Pour découvrir les assemblys de démarrage d’hébergement chargé par l’application ou par des bibliothèques, activez la journalisation, puis vérifiez les journaux des applications. Les erreurs qui se produisent lors du chargement d’assemblys sont enregistrées. Assemblys de démarrage hébergement chargés sont enregistrées au niveau du débogage, et toutes les erreurs sont journalisées.

Les lectures d’application exemple le le [HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey) dans un `string` de tableau et affiche le résultat dans la page d’Index de l’application :

[!code-csharp[Main](ihostingstartup/sample/HostingStartupSample/Pages/Index.cshtml.cs?name=snippet1&highlight=14-16)]

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Désactiver le chargement automatique des assemblys de démarrage d’hébergement

Il existe deux méthodes pour désactiver le chargement automatique des assemblys de démarrage d’hébergement :

* Définir le [empêcher le démarrage d’hébergement](xref:fundamentals/hosting#prevent-hosting-startup) héberger le paramètre de configuration.
* Définir le `ASPNETCORE_preventHostingStartup` variable d’environnement.

Lorsque le paramètre d’hôte ou de la variable d’environnement est définie sur `true` ou `1`, l’hébergement des assemblys de démarrage ne sont pas chargés automatiquement. Si les deux sont définis, le paramètre de l’hôte détermine le comportement.

Désactivation des assemblys de démarrage hébergement à l’aide de la variable d’environnement ou le paramètre hôte les désactive globalement et peut désactiver plusieurs fonctionnalités d’une application. Il n’est pas encore possible de désactiver de manière sélective un hébergement assembly de démarrage ajouté par une bibliothèque, à moins que la bibliothèque propose sa propre option de configuration. Une version ultérieure propose la possibilité de désactiver de manière sélective les assemblys de démarrage hébergement (consultez [GitHub émettre aspnet/hébergement # entré 1243](https://github.com/aspnet/Hosting/pull/1243)).

## <a name="implement-ihostingstartup-features"></a>Implémenter des fonctionnalités de IHostingStartup

### <a name="create-the-assembly"></a>Créer l’assembly

Un `IHostingStartup` déployée en tant qu’assembly basé sur une application console sans point d’entrée. Les références d’assembly le [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package :

[!code-xml[Main](ihostingstartup/snapshot_sample/StartupFeature.csproj)]

A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribut identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost). Dans l’exemple suivant, l’espace de noms est `StartupFeature`, et la classe est `StartupFeatureHostingStartup`:

[!code-csharp[Main](ihostingstartup/snapshot_sample/StartupFeature.cs?name=snippet1)]

Une classe implémente `IHostingStartup`. La classe [configurer](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) utilise un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) pour ajouter des fonctionnalités à une application :

[!code-csharp[Main](ihostingstartup/snapshot_sample/StartupFeature.cs?name=snippet2&highlight=3,5)]

Lors de la génération un `IHostingStartup` de projet, le fichier de dépendances (*\*. deps.json*) définit les `runtime` l’emplacement de l’assembly à la *bin* dossier :

[!code-json[Main](ihostingstartup/snapshot_sample/StartupFeature1.deps.json?range=2-13&highlight=8)]

Seule une partie du fichier est affichée. Le nom de l’assembly dans l’exemple est `StartupFeature`.

### <a name="update-the-dependencies-file"></a>Mettre à jour le fichier de dépendances

L’emplacement d’exécution est spécifié dans le  *\*. deps.json* fichier. Pour active la fonctionnalité, le `runtime` élément doit spécifier l’emplacement de l’assembly du runtime de la fonctionnalité. Préfixe la `runtime` emplacement avec `lib/netcoreapp2.0/`:

[!code-json[Main](ihostingstartup/snapshot_sample/StartupFeature2.deps.json?range=2-13&highlight=8)]

Dans l’exemple d’application, la modification de la  *\*. deps.json* fichier est effectué par un [PowerShell](/powershell/scripting/powershell-scripting) script. Le script PowerShell est automatiquement déclenché par une cible de génération dans le fichier projet.

### <a name="feature-activation"></a>Activation de fonctionnalités

**Placez le fichier d’assembly**

Le `IHostingStartup` fichier d’assembly de l’implémentation doit être *bin*-déployés dans l’application ou placé dans le [magasin runtime](/dotnet/core/deploying/runtime-store):

Pour une utilisation par l’utilisateur, placez l’assembly dans le magasin de runtime du profil utilisateur à :

```
<DRIVE>\Users\<USER>\.dotnet\store\x64\netcoreapp2.0\<FEATURE_ASSEMBLY_NAME>\<FEATURE_VERSION>\lib\netcoreapp2.0\
```

Dans le monde entier, placez l’assembly dans le magasin d’exécution de l’installation de .NET Core :

```
<DRIVE>\Program Files\dotnet\store\x64\netcoreapp2.0\<FEATURE_ASSEMBLY_NAME>\<FEATURE_VERSION>\lib\netcoreapp2.0\
```

Lors du déploiement de l’assembly dans le magasin de l’exécution, le fichier de symboles peut-être être déployé également, mais n’est pas nécessaire pour la fonctionnalité soit opérationnelle.

**Placez le fichier de dépendances**

La mise en oeuvre  *\*. deps.json* fichier doit être dans un emplacement accessible.

Pour une utilisation par l’utilisateur, placez le fichier dans le `additonalDeps` dossier du profil utilisateur `.dotnet` paramètres : 

```
<DRIVE>\Users\<USER>\.dotnet\x64\additionalDeps\<FEATURE_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\2.0.0\
```

Dans le monde entier, placez le fichier dans le `additonalDeps` dossier de l’installation de .NET Core :

```
<DRIVE>\Program Files\dotnet\additionalDeps\<FEATURE_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\2.0.0\
```

Notez le numéro de version, `2.0.0`, reflète la version du runtime partagé utilisé par l’application cible. Le runtime partagé est indiqué dans le  *\*. runtimeconfig.json* fichier. Dans l’exemple d’application, le runtime partagé est spécifié dans le *HostingStartupSample.runtimeconfig.json* fichier.

**Jeu de variables d’environnement**

Définissez les variables d’environnement suivantes dans le contexte de l’application qui utilise la fonctionnalité.

ASPNETCORE\_HOSTINGSTARTUPASSEMBLIES

Seuls les assemblys démarrage hébergement sont analysés pour la `HostingStartupAttribute`. Le nom de l’assembly de l’implémentation est fourni dans cette variable d’environnement. L’exemple d’application définit cette valeur sur `StartupDiagnostics`.

La valeur peut également être définie à l’aide de la [hébergeant les assemblys de démarrage](xref:fundamentals/hosting#hosting-startup-assemblies) héberger le paramètre de configuration.

DOTNET\_SUPPLÉMENTAIRES\_DÉP.

L’emplacement de la mise en oeuvre  *\*. deps.json* fichier.

Si le fichier est placé dans le profil d’utilisateur *.dotnet* dossier pour une utilisation de l’utilisateur :

```
<DRIVE>\Users\<USER>\.dotnet\x64\additionalDeps\
```

Si le fichier est placé dans l’installation de .NET Core dans le monde entier, fournissez le chemin d’accès complet au fichier :

```
<DRIVE>\Program Files\dotnet\additionalDeps\<FEATURE_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\2.0.0\<FEATURE_ASSEMBLY_NAME>.deps.json
```

L’exemple d’application définit cette valeur sur :

```
%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\
```

Pour obtenir des exemples montrant comment définir des variables d’environnement pour les différents systèmes d’exploitation, consultez [fonctionne avec plusieurs environnements](xref:fundamentals/environments).

## <a name="sample-app"></a>Exemple d’application

Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/ihostingstartup/sample/) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) utilise `IHostingStartup` pour créer un outil de diagnostic. L’outil ajoute deux middlewares à l’application au démarrage, qui fournissent des informations de diagnostic :

* Services enregistrés
* Adresse : schéma, hôte, chemin d’accès de base, chemin d’accès, chaîne de requête
* Connexion : adresse IP distante, port distant, adresse IP locale, port local, le certificat client
* En-têtes de demande
* Variables d’environnement

Pour exécuter l’exemple :

1. Le projet de démarrage du Diagnostic utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier son *StartupDiagnostics.deps.json* fichier. PowerShell est installé par défaut sur le système d’exploitation Windows depuis Windows 7 SP1 et Windows Server 2008 R2 SP1. Pour obtenir de PowerShell sur d’autres plateformes, consultez [installation de Windows PowerShell](/powershell/scripting/setup/installing-windows-powershell).
2. Générez le projet de démarrage du Diagnostic. Une cible de génération dans le fichier projet :
   * Déplace l’assembly et des symboles des fichiers au magasin de runtime du profil utilisateur.
   * Déclenche le script PowerShell pour modifier la *StartupDiagnostics.deps.json* fichier.
   * Déplace le *StartupDiagnostics.deps.json* fichier pour le profil d’utilisateur `additionalDeps` dossier.
3. Définissez les variables d’environnement :
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`: `StartupDiagnostics`
    * `DOTNET_ADDITIONAL_DEPS`: `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`
4. Exécutez l’exemple d’application.
5. Demander le `/services` point de terminaison pour voir l’application inscrit des services. Demander le `/diag` point de terminaison pour afficher les informations de diagnostic.
