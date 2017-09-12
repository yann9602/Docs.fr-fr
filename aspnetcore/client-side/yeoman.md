---
title: "Génération de projets avec Yeoman dans ASP.NET Core"
author: spboyer
description: "Cet article Guide de création d’application web à l’aide de la Yeoman une ASP.NET Core générateur sur macOS."
keywords: ASP.NET Core, Yeoman, multiplateforme, v aspnet
ms.author: spboyer
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
ms.openlocfilehash: 61561b55774faf375090c92b574a64f1a12f9647
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a>Introduction à la génération de projets avec Yeoman dans ASP.NET Core

[Yeoman](http://yeoman.io/) est un système de la structure de projet pour la création de nombreux types d’applications. Le Yeoman générateur pour ASP.NET Core contient une variété de modèles de projet pour démarrer un nouveau site web, MVC ou application console.

## <a name="install-nodejs-npm-and-yeoman"></a>Installation de Node.js et npm Yeoman

### <a name="prerequisites"></a>Conditions préalables

Node.js et npm sont requis pour Yeoman. Télécharger à partir de [Node.js](https://nodejs.org/). Le programme d’installation inclut [Node.js](https://nodejs.org/) et [npm](https://www.npmjs.com/). Bower est également requis pour l’installation des infrastructures d’interface utilisateur tels que les données d’amorçage.

Pour installer Yeoman et Bower, exécutez la commande suivante :

```console
npm install -g yo bower
```

>[!Note]
>Si vous obtenez l’erreur `npm ERR! Please try running this command again as root/Administrator.` sur macOS, exécutez la commande suivante à l’aide [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`

À partir d’une invite de commandes, installez le générateur ASP.NET :

```console
npm install -g generator-aspnet
```

> [!NOTE]
> Si vous obtenez une erreur d’autorisation, exécutez la commande sous `sudo` comme décrit ci-dessus.

Le `–g` indicateur installe le Générateur de manière globale, afin qu’il peut être utilisé à partir de n’importe quel chemin d’accès.

## <a name="create-an-aspnet-app"></a>Créer une application ASP.NET

Exécutez le générateur ASP.NET basées sur Yeoman :

```console
yo aspnet
```

Le générateur affiche un menu. Flèche vers le bas pour le **Web Application base [sans l’appartenance et l’autorisation]** de projet, puis appuyez sur **entrée**:

![Fenêtre de commande : quel type d’application voulez-vous créer ? Menu de types d’applications](yeoman/_static/yeoman-yo-aspnet.png)

Sélectionnez les données d’amorçage en tant que l’infrastructure d’interface utilisateur, puis appuyez sur **entrée**.

Utilisez «**MyWebApp**» pour le nom de l’application, puis sur **entrée**.

Yeoman sera structurer le projet et ses fichiers de prise en charge. Étapes suggérées sont également fournis sous la forme de commandes.

![Fenêtre de commande : quel est le nom de votre application ASP.NET ? Invite de commandes](yeoman/_static/yeoman-yo-aspnet-created.png)

Le [ASP.NET Générateur](https://www.npmjs.com/package/generator-aspnet) crée des projets qui peuvent être chargé dans le Code de Visual Studio, Visual Studio, ou exécuter à partir de la ligne de commande ASP.NET Core.

## <a name="restore-build-and-run"></a>Restaurer, générer et exécuter

Suivez les commandes suggérées en modifiant les répertoires dans lesquels le `MyWebApp` active. Puis exécutez `dotnet restore`.

![Commande (fenêtre)](yeoman/_static/dotnet-restore.png)

Générer et exécuter l’application à l’aide de `dotnet build` et `dotnet run`:

![Commande (fenêtre)](yeoman/_static/dotnet-build-run.png)

À ce stade, vous pouvez naviguer vers l’URL affichée pour tester l’application ASP.NET Core nouvellement créé.

## <a name="client-side-packages"></a>Packages côté client

Les ressources frontaux sont fournies par les modèles à partir de la Yeoman à l’aide de générateur le [Bower](xref:client-side/bower) le Gestionnaire de package de côté client, ajout *bower.json* et *.bowerrc* fichiers à restaurer les packages côté client à l’aide de Bower.

Le [BundlerMinifier](xref:client-side/bundling-and-minification) composant est également inclus par défaut pour la facilité de concaténation (regroupement) et la réduction de CSS, JavaScript et HTML.

## <a name="building-and-running-from-visual-studio"></a>Création et l’exécution à partir de Visual Studio

Vous pouvez charger votre projet de web ASP.NET Core généré directement dans Visual Studio, puis générer et exécuter votre projet à partir de là. Suivez les instructions ci-dessus pour structurer une application ASP.NET Core à l’aide de Yeoman. Cette fois-ci, choisissez **Application Web** dans le menu et le nom de l’application `MyWebApp`.

Ouvrez Visual Studio. Dans le menu fichier, sélectionnez Ouvrir ‣ projet/Solution.

Dans la boîte de dialogue Ouvrir un projet, accédez à la *.csproj* de fichier, sélectionnez-le, puis cliquez sur le **ouvrir** bouton. Dans l’Explorateur de solutions, le projet doit ressembler à la capture d’écran ci-dessous.

![Fichiers et dossiers d’un nouveau projet dans l’Explorateur de solutions](yeoman/_static/yeoman-solution.png)

Yeoman micro-capsules une application web MVC, support de build côté serveur et côté client avec les deux. Dépendances de côté serveur sont répertoriés sous la **dépendances/NuGet** nœud et des dépendances côté client dans le **dépendances/Bower** nœud de l’Explorateur de solutions. Dépendances soient restaurés automatiquement lorsque le projet est chargé.

![Sous le nœud dépendances dans l’arborescence de l’Explorateur de solutions, le dossier Bower est ouvert de liste de ses dépendances.](yeoman/_static/yeoman-loading-dependencies.png)

Lorsque toutes les dépendances sont restaurés, appuyez sur **F5** pour exécuter le projet. La page d’accueil par défaut s’affiche dans le navigateur.

![Application Web ouverte dans Microsoft Edge](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a>Restauration, de la création et l’hébergement d’une ligne de commande

Vous pouvez préparer et héberger votre application web à l’aide de l’interface de ligne de base de .NET.

À une invite de commandes, accédez au répertoire en cours dans le dossier contenant le projet (autrement dit, le dossier contenant le *.csproj* fichier) :

```console
cd src\MyWebApp
```

Restaurer les dépendances du package NuGet du projet :

```console
dotnet restore
```

Exécutez l’application :

```console
dotnet run
```

L’inter-plateformes [Kestrel](xref:fundamentals/servers/kestrel) serveur web commencera à l’écoute sur le port 5000.

Ouvrez un navigateur web et accédez à `http://localhost:5000`.

![Application Web ouverte dans Microsoft Edge](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a>Ajout à votre projet avec des générateurs de sub

À l’aide de Yeoman [sub générateurs](https://github.com/omnisharp/generator-aspnet), vous pouvez ajouter un `nuget.config` ou un `web.config` après la création du projet. Par exemple, exécutez la commande suivante à partir du répertoire dans lequel le fichier doit être créé :

```console
yo aspnet:nugetconfig
```

Le résultat est un fichier de configuration NuGet nommé `nuget.config` avec le contenu suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Serveurs (Kestrel et WebListener)](xref:fundamentals/servers/index)
* [Notions de base](xref:fundamentals/index)
