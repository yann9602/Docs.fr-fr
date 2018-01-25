---
title: "Utilisez le modèle de projet React"
author: SteveSandersonMS
description: "Découvrez comment démarrer avec le modèle de projet ASP.NET Core seule Page Application (SPA) release candidate pour réagir et application de réagir créer."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 12/06/2017
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: spa/react
ms.openlocfilehash: aa70a35ad938fff6911367ee9d12aac9d575be7e
ms.sourcegitcommit: efc9e5b5fffa0e13957131a0da52cc1532a87651
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="use-the-react-project-template-release-candidate"></a>Utilisez le modèle de projet React (version finale)

> [!NOTE]
> Cette documentation n’est pas sur le modèle de projet React finale. **Cette documentation est sur la version release candidate du modèle React.** Nous espérons qu’expédier la version finale dans 2018 anticipée.

Le modèle de projet React mis à jour fournit un point de départ pratique pour ASP.NET Core des applications à l’aide de réagir et [application react créer](https://github.com/facebookincubator/create-react-app) conventions (ARC) pour implémenter une interface utilisateur côté client riche (IU).

Le modèle est équivalent à la création d’un projet ASP.NET Core pour agir comme un serveur principal d’API et qu’un projet standard arc réagir à agir comme une interface utilisateur, mais avec la commodité d’hébergement à la fois dans un projet d’application unique qui peut être créé et publié comme une unité unique.

## <a name="create-a-new-app"></a>Créer une application

Pour commencer, assurez-vous que vous avez [installé le modèle de projet mis à jour React](xref:spa/index#installation). Ces instructions ne s’appliquent pas au modèle de projet précédent React inclus dans le .NET Core 2.0.x SDK.

Créez un nouveau projet à partir d’une invite de commandes à l’aide de la commande `dotnet new react` dans un répertoire vide. Par exemple, les commandes suivantes créent l’application dans un *application mon nouveau* active et basculer vers ce répertoire :

```console
dotnet new react -o my-new-app
cd my-new-app
```

Exécutez l’application à partir de Visual Studio ou le .NET Core CLI :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez généré *.csproj* , et exécutez l’application normalement à partir de là.

Le processus de génération restaure npm dépendent de la première exécution, ce qui peut prendre plusieurs minutes. Les builds suivantes sont beaucoup plus rapides.

# <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Assurez-vous d’avoir une variable d’environnement appelée `ASPNETCORE_Environment` avec la valeur `Development`. Sur Windows (dans les invites de non-PowerShell), exécutez `SET ASPNETCORE_Environment=Development`. Sur Linux ou macOS, exécutez `export ASPNETCORE_Environment=Development`.

Exécutez `dotnet build` pour vérifier votre application se génère correctement. Sur la première exécution, le processus de génération restaure les dépendances de npm, qui peuvent prendre plusieurs minutes. Les builds suivantes sont beaucoup plus rapides.

Exécutez `dotnet run` pour démarrer l’application.

---

Le modèle de projet crée une application ASP.NET Core et une application de réagir. L’application ASP.NET Core est destinée à être utilisé pour l’accès aux données, l’autorisation et autres problèmes côté serveur. L’application de réagir résidant dans le *ClientApp* sous-répertoire, est destiné à être utilisé pour tous les problèmes de l’interface utilisateur.

## <a name="add-pages-images-styles-modules-etc"></a>Ajouter des pages, les images, les styles, les modules, etc.

Le *ClientApp* active est une application de réagir de l’arc standard. Consultez officielle [documentation de l’arc](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) pour plus d’informations.

Il existe de légères différences entre l’application de réagir créés par ce modèle et celui créé par arc lui-même ; Toutefois, les fonctionnalités de l’application sont identiques. L’application créée par le modèle contient un [Bootstrap](https://getbootstrap.com/)-en fonction de la mise en page et un exemple de routage de base.

## <a name="install-npm-packages"></a>Installer les packages npm

Pour installer des packages tiers npm, utilisez une invite de commandes dans le *ClientApp* sous-répertoire. Exemple :

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publier et déployer

Dans le développement, l’application s’exécute en mode optimisé pour des raisons pratiques de développement. Par exemple, JavaScript offres incluent des mappages de sources (de sorte que lors du débogage, vous pouvez voir votre code source d’origine). L’application surveille JavaScript, HTML et CSS de modifications de fichiers sur disque et automatiquement recompile et recharge lorsqu’il rencontre ces fichiers modifier.

Dans la production, ont une version de votre application qui est optimisée pour les performances. Il est configuré pour se produire automatiquement. Lorsque vous publiez, la configuration de build émet une build transpiled réduite, de votre code côté client. Contrairement à la version de développement, la build de production ne requiert pas Node.js être installé sur le serveur.

Vous pouvez utiliser standard [méthodes de déploiement et d’hébergement ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Exécuter le serveur de l’arc indépendamment

Le projet est configuré pour démarrer sa propre instance du serveur de développement arc en arrière-plan lorsque l’application ASP.NET Core démarre en mode de développement. Ceci est pratique car elle signifie que vous n’êtes pas obligé d’exécuter un serveur distinct manuellement.

Il existe un inconvénient de ce programme d’installation par défaut. Chaque fois que vous modifiez votre code c# et base ASP.NET application doit redémarrer, le serveur de l’arc redémarre. Quelques secondes sont requises pour démarrer la sauvegarde. Si vous apportez des modifications de code c# fréquentes et que vous ne souhaitez pas attendre que le serveur de l’arc à redémarrer, exécutez le serveur d’arc en externe, indépendamment du processus ASP.NET Core. Pour cela :

1. Dans une invite de commandes, basculez vers le *ClientApp* sous-répertoire et lancer le serveur de développement arc :

    ```console
    cd ClientApp
    npm start
    ```

2. Modifiez votre application ASP.NET Core pour utiliser l’instance de serveur arc externe au lieu de lancer un de ses propres. Dans votre *démarrage* de classe, remplacez le `spa.UseReactDevelopmentServer` invocation avec les éléments suivants :

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Lorsque vous démarrez votre application ASP.NET Core, il ne sera pas lancer un serveur de l’arc. L’instance que vous avez démarré manuellement est utilisé à la place. Cela lui permet de démarrer et redémarrer plus rapidement. Il n’attend plus pour votre application de réagir à reconstruire chaque fois.
