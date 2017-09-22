---
title: "Structure de répertoire ASP.NET Core"
author: guardrex
description: "La structure de répertoires des applications ASP.NET Core publiées."
keywords: "ASP.NET Core, structure de répertoires"
ms.author: riande
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: e55eb131-d42e-4bf6-b130-fd626082243c
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/directory-structure
ms.openlocfilehash: 9ec635b596520e3d19f4040758dd59c1cbca97f4
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a>Structure de répertoires d’applications ASP.NET Core publiées

Par [Luke Latham](https://github.com/GuardRex)

Dans ASP.NET Core, le répertoire de l’application, *publier*, est constitué de fichiers d’application, les fichiers de configuration, les ressources statiques, packages et l’exécution (pour les applications autonomes). Il s’agit de la même structure de répertoire que les versions précédentes d’ASP.NET, où l’application entière réside à l’intérieur du répertoire web racine.

| Type d’application | Structure de répertoires |
| --- | --- |
| Déploiement d’infrastructure dépendant | <ul><li>publier\*<ul><li>journaux\* (s’il est inclus dans publishOptions)</li><li>Refs\*</li><li>runtimes\*</li><li>Vues\* (s’il est inclus dans publishOptions)</li><li>wwwroot\* (s’il est inclus dans publishOptions)</li><li>fichiers .dll</li><li>MyApp.DEPS.JSON</li><li>MyApp.dll</li><li>MyApp.pdb</li><li>MyApp. PrecompiledViews.dll (si la précompilation des vues Razor)</li><li>MyApp. PrecompiledViews.pdb (si la précompilation des vues Razor)</li><li>MyApp.runtimeconfig.JSON</li><li>Web.config (s’il est inclus dans publishOptions)</li></ul></li></ul> |
| Déploiement autonome | <ul><li>publier\*<ul><li>journaux\* (s’il est inclus dans publishOptions)</li><li>Refs\*</li><li>Vues\* (s’il est inclus dans publishOptions)</li><li>wwwroot\* (s’il est inclus dans publishOptions)</li><li>fichiers .dll</li><li>MyApp.DEPS.JSON</li><li>MyApp.exe</li><li>MyApp.pdb</li><li>MyApp. PrecompiledViews.dll (si la précompilation des vues Razor)</li><li>MyApp. PrecompiledViews.pdb (si la précompilation des vues Razor)</li><li>MyApp.runtimeconfig.JSON</li><li>Web.config (s’il est inclus dans publishOptions)</li></ul></li></ul> |
\*Indique un répertoire

Le contenu de la *publier* répertoire représente le *chemin d’accès racine du contenu*, également appelé le *chemin de base d’application*, du déploiement. Le nom est donné à la *publier* active dans le déploiement, son emplacement sert de chemin d’accès physique à celle du serveur à l’application hébergée. Le *wwwroot* active, le cas échéant, contient uniquement des éléments statiques. Le *journaux* répertoire peut être inclus dans le déploiement en créant dans le projet et en ajoutant le `<Target>` élément ci-dessous pour votre *.csproj* fichier ou en créer physiquement le répertoire sur le serveur.

```xml
<Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
  <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
  <MakeDir Directories="$(PublishUrl)Logs" Condition="!Exists('$(PublishUrl)Logs')" />
</Target>
```

La première `<MakeDir>` élément, qui utilise le `PublishDir` propriété, est utilisée par l’interface de ligne de base de .NET pour déterminer l’emplacement cible pour l’opération de publication. La seconde `<MakeDir>` élément, qui utilise le `PublishUrl` propriété, est utilisée par Visual Studio pour déterminer l’emplacement cible. Visual Studio utilise le `PublishUrl` propriété pour la compatibilité avec les projets non .NET Core.

Le répertoire de déploiement requiert des autorisations de lecture et d’exécution, tandis que la *journaux* directory nécessite des autorisations de lecture/écriture. Autres répertoires où seront écrit les ressources nécessitent des autorisations de lecture/écriture.
