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
ms.openlocfilehash: 60797bff85a44dd10caad4aabc109ee12dedfe61
ms.sourcegitcommit: 7efdc4b6025ad70c15c26bf7451c3c0411123794
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a>Structure de répertoires d’applications ASP.NET Core publiées

Par [Luke Latham](https://github.com/guardrex)

Dans ASP.NET Core, le répertoire de l’application, *publier*, est constitué de fichiers d’application, les fichiers de configuration, les ressources statiques, packages et l’exécution (pour les applications autonomes). Il s’agit de la même structure de répertoire que les versions précédentes d’ASP.NET, où l’application entière réside à l’intérieur du répertoire web racine.

| Type d’application | Structure de répertoires |
| --- | --- |
| Déploiement d’infrastructure dépendant | <ul><li>publier\*<ul><li>journaux\* (s’il est inclus dans publishOptions)</li><li>Refs\*</li><li>runtimes\*</li><li>Vues\* (s’il est inclus dans publishOptions)</li><li>wwwroot\* (s’il est inclus dans publishOptions)</li><li>fichiers .dll</li><li>MyApp.DEPS.JSON</li><li>MyApp.dll</li><li>MyApp.pdb</li><li>MyApp. PrecompiledViews.dll (si la précompilation des vues Razor)</li><li>MyApp. PrecompiledViews.pdb (si la précompilation des vues Razor)</li><li>MyApp.runtimeconfig.JSON</li><li>Web.config (s’il est inclus dans publishOptions)</li></ul></li></ul> |
| Déploiement autonome | <ul><li>publier\*<ul><li>journaux\* (s’il est inclus dans publishOptions)</li><li>Refs\*</li><li>Vues\* (s’il est inclus dans publishOptions)</li><li>wwwroot\* (s’il est inclus dans publishOptions)</li><li>fichiers .dll</li><li>MyApp.DEPS.JSON</li><li>MyApp.exe</li><li>MyApp.pdb</li><li>MyApp. PrecompiledViews.dll (si la précompilation des vues Razor)</li><li>MyApp. PrecompiledViews.pdb (si la précompilation des vues Razor)</li><li>MyApp.runtimeconfig.JSON</li><li>Web.config (s’il est inclus dans publishOptions)</li></ul></li></ul> |
\*Indique un répertoire

Le contenu de la *publier* répertoire représente le *chemin d’accès racine du contenu*, également appelé le *chemin de base d’application*, du déploiement. Le nom est donné à la *publier* active dans le déploiement, son emplacement sert de chemin d’accès physique à celle du serveur à l’application hébergée. Le *wwwroot* active, le cas échéant, contient uniquement des éléments statiques. Le *journaux* répertoire peut être inclus dans le déploiement en créant dans le projet et en ajoutant le `<Target>` élément ci-dessous pour votre *.csproj* fichier ou en créer physiquement le répertoire sur le serveur.

```xml
<Target Name="CreateLogsFolder" AfterTargets="Publish">
  <MakeDir Directories="$(PublishDir)Logs" 
           Condition="!Exists('$(PublishDir)Logs')" />
  <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                    Lines="Generated file" 
                    Overwrite="True" 
                    Condition="!Exists('$(PublishDir)Logs\.log')" />
</Target>
```

Le `<MakeDir>` élément crée vide *journaux* dossier dans le projet publié. L’élément utilise le `PublishDir` propriété pour déterminer l’emplacement cible pour la création du dossier. Plusieurs méthodes de déploiement, telles que Web Deploy, ignorent les dossiers vides pendant le déploiement. Le `<WriteLinesToFile>` élément génère un fichier dans le *journaux* dossier, ce qui garantit le déploiement du dossier sur le serveur. Notez que la création du dossier peut-être encore échouer si le processus de travail n’a pas accès en écriture dans le dossier cible.

Le répertoire de déploiement requiert des autorisations de lecture et d’exécution, tandis que la *journaux* directory nécessite des autorisations de lecture/écriture. Autres répertoires où seront écrit les ressources nécessitent des autorisations de lecture/écriture.
