---
title: "Utilisez le modèle de projet angulaire"
author: SteveSandersonMS
description: "Découvrez comment démarrer avec le modèle de projet ASP.NET Core seule Page Application (SPA) release candidate pour angulaire et l’interface CLI angulaire."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 12/06/2017
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: spa/angular
ms.openlocfilehash: 4162b1c26e9d278c811f691c4277d4de25adb204
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-angular-project-template-release-candidate"></a>Utilisez le modèle de projet angulaire (version finale)

> [!NOTE]
> Cette documentation n’est pas sur le modèle de projet angulaire finale. **Cette documentation est sur la version release candidate du modèle angulaire.** Nous espérons qu’expédier la version finale dans 2018 anticipée.

Le modèle de projet angulaire mis à jour fournit un point de départ pratique pour ASP.NET Core applications à l’aide de 5 angulaire et l’interface CLI angulaire pour implémenter une interface utilisateur côté client riche (IU).

Le modèle est équivalent à la création d’un projet ASP.NET Core pour agir comme un serveur principal d’API et un projet CLI angulaire d’agir comme une interface utilisateur. Le modèle offre l’avantage d’héberger les deux types de projets dans un projet d’application unique. Par conséquent, le projet d’application peut être généré et publié sous la forme d’une unité unique.

## <a name="create-a-new-app"></a>Créer une application

Pour commencer, assurez-vous que vous avez [installé le modèle de projet angulaire mis à jour](xref:spa/index#installation). Ces instructions ne s’appliquent pas au modèle de projet angulaire précédente inclus dans le .NET Core 2.0.x SDK.

Créez un nouveau projet à partir d’une invite de commandes à l’aide de la commande `dotnet new angular` dans un répertoire vide. Par exemple, les commandes suivantes créent l’application dans un *application mon nouveau* active et basculer vers ce répertoire :

```console
dotnet new angular -o my-new-app
cd my-new-app
```

Exécutez l’application à partir de Visual Studio ou le .NET Core CLI :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez généré *.csproj* , et exécutez l’application normalement à partir de là.

Le processus de génération restaure npm dépendent de la première exécution, ce qui peut prendre plusieurs minutes. Les builds suivantes sont beaucoup plus rapides.

# <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Assurez-vous d’avoir une variable d’environnement appelée `ASPNETCORE_Environment` avec la valeur `Development`. Sur Windows (dans les invites de non-PowerShell), exécutez `SET ASPNETCORE_Environment=Development`. Sur Linux ou macOS, exécutez `export ASPNETCORE_Environment=Development`.

Exécutez `dotnet build` pour vérifier l’application se génère correctement. Sur la première exécution, le processus de génération restaure les dépendances de npm, qui peuvent prendre plusieurs minutes. Les builds suivantes sont beaucoup plus rapides.

Exécutez `dotnet run` pour démarrer l’application. Un message semblable au suivant est enregistré :

```console
Now listening on: http://localhost:<port>
```

Accédez à cette URL dans un navigateur.

L’application démarre une instance du serveur angulaire CLI en arrière-plan. Un message semblable au suivant est consigné : *NG Live un serveur de développement est à l’écoute sur localhost :&lt;otherport&gt;, ouvrez votre navigateur sur http://localhost :&lt;otherport&gt; /* . Ignorer ce message&mdash;il a **pas** l’URL de l’application ASP.NET Core et CLI angulaire combinée.

---

Le modèle de projet crée une application ASP.NET Core et une application angulaire. L’application ASP.NET Core est destinée à être utilisé pour l’accès aux données, l’autorisation et autres problèmes côté serveur. L’application angulaire, résidant dans le *ClientApp* sous-répertoire, est destiné à être utilisé pour tous les problèmes de l’interface utilisateur.

## <a name="add-pages-images-styles-modules-etc"></a>Ajouter des pages, les images, les styles, les modules, etc.

Le *ClientApp* répertoire contient une application angulaire CLI standard. Consultez officielle [documentation angulaire](https://github.com/angular/angular-cli/wiki) pour plus d’informations.

Il existe de légères différences entre l’application angulaire créés par ce modèle et celui créé par angulaire CLI lui-même (via `ng new`) ; Toutefois, les fonctionnalités de l’application sont identiques. L’application créée par le modèle contient un [Bootstrap](https://getbootstrap.com/)-en fonction de la mise en page et un exemple de routage de base.

## <a name="run-ng-commands"></a>Exécutez les commandes ng

Dans une invite de commandes, basculez vers le *ClientApp* sous-répertoire :

```console
cd ClientApp
```

Si vous avez le `ng` outil installé de manière globale, vous pouvez exécuter une de ses commandes. Par exemple, vous pouvez exécuter `ng lint`, `ng test`, ou toute autre [commandes CLI angulaire](https://github.com/angular/angular-cli/wiki#additional-commands). Il est inutile d’exécuter `ng serve` , car votre application ASP.NET Core porte servant à la fois côté client et côté serveur des parties de votre application. En interne, il utilise `ng serve` dans le développement.

Si vous n’avez pas les `ng` outil est installé, exécutez `npm run ng` à la place. Par exemple, vous pouvez exécuter `npm run ng lint` ou `npm run ng test`.

## <a name="install-npm-packages"></a>Installer les packages npm

Pour installer des packages tiers npm, utilisez une invite de commandes dans le *ClientApp* sous-répertoire. Exemple :

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publier et déployer

Dans le développement, l’application s’exécute en mode optimisé pour des raisons pratiques de développement. Par exemple, JavaScript offres incluent des mappages de sources (de sorte que lors du débogage, vous pouvez voir votre code TypeScript d’origine). L’application surveille les modifications de fichier TypeScript, HTML et CSS sur le disque et recompile automatiquement et recharge lorsqu’il rencontre ces fichiers modifier.

Dans la production, ont une version de votre application qui est optimisée pour les performances. Il est configuré pour se produire automatiquement. Lorsque vous publiez, la configuration de build émet un réduite, anticipée de temps (AOA) compilés build de votre code côté client. Contrairement à la version de développement, la build de production ne nécessite pas Node.js être installé sur le serveur (sauf si vous avez activé [côté serveur pré-rendu](#server-side-rendering)).

Vous pouvez utiliser standard [méthodes de déploiement et d’hébergement ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-ng-serve-independently"></a>Exécutez « ng serve » indépendamment

Le projet est configuré pour démarrer sa propre instance du serveur angulaire CLI en arrière-plan lorsque l’application ASP.NET Core démarre en mode de développement. Ceci est pratique, car vous n’êtes pas obligé d’exécuter un serveur distinct manuellement.

Il existe un inconvénient de ce programme d’installation par défaut. Chaque fois que vous modifiez votre code c# et base ASP.NET application doit redémarrer, le serveur CLI angulaire redémarre. Environ 10 secondes est requis pour démarrer la sauvegarde. Si vous apportez des modifications de code c# fréquentes et que vous ne souhaitez pas attendre CLI angulaire à redémarrer, exécutez le serveur angulaire CLI en externe, indépendamment du processus ASP.NET Core. Pour cela :

1. Dans une invite de commandes, basculez vers le *ClientApp* sous-répertoire et lancer le serveur de développement angulaire CLI :

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Utilisez `npm start` pour lancer le serveur de développement CLI angulaire ne pas `ng serve`, de sorte que la configuration dans *package.json* est respectée. Pour passer des paramètres supplémentaires au serveur CLI angulaire, ajoutez-les à la `scripts` de ligne dans votre *package.json* fichier.

2. Modifiez votre application ASP.NET Core pour utiliser l’instance CLI angulaire externe au lieu de lancer un de ses propres. Dans votre *démarrage* de classe, remplacez le `spa.UseAngularCliServer` invocation avec les éléments suivants :

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Lorsque vous démarrez votre application ASP.NET Core, il ne sera pas lancer un serveur CLI angulaire. L’instance que vous avez démarré manuellement est utilisé à la place. Cela lui permet de démarrer et redémarrer plus rapidement. Il attend n’est plus angulaire CLI régénérer la chaque fois que votre application cliente.

## <a name="server-side-rendering"></a>Rendu côté serveur

Une fonctionnalité de performances, vous pouvez choisir pour les rendre votre application angulaire sur le serveur, ainsi que son exécution sur le client. Cela signifie que les navigateurs recevront balisage HTML qui représente l’interface utilisateur initiale de votre application, de façon à afficher même avant le téléchargement et l’exécution de vos groupes de JavaScript. La plupart de l’implémentation de ce provient d’une fonctionnalité angulaire appelée [angulaire universelle](https://universal.angular.io/).

> [!TIP]
> L’activation de rendu côté serveur (SSR) présente un nombre de complications supplémentaires à la fois au cours de développement et de déploiement. Lecture [présente deux inconvénients : SSR](#drawbacks-of-ssr) pour déterminer si SSR est adapté à vos besoins.

Pour activer SSR, vous devez effectuer un certain nombre d’ajouts à votre projet.

Dans le *démarrage* (classe), *après* la ligne qui configure `spa.Options.SourcePath`, et *avant* l’appel à `UseAngularCliServer` ou `UseProxyToSpaDevelopmentServer`, ajoutez le code suivant :

[!code-csharp[](sample/AngularServerSideRendering/Startup.cs?name=snippet_Call_UseSpa&highlight=5-12)]

En mode de développement, ce code tente de créer le groupe de SSR en exécutant le script `build:ssr`, qui est défini dans *ClientApp\package.json*. Cette opération génère une application angulaire nommée `ssr`, qui n’est pas encore défini. 

À la fin de la `apps` tableau *ClientApp/.angular-cli.json*, définir une application supplémentaire avec le nom `ssr`. Utilisez les options suivantes :

[!code-json[](sample/AngularServerSideRendering/ClientApp/.angular-cli.json?range=24-41)]

Cette nouvelle configuration de l’application SSR requiert deux fichiers supplémentaires : *tsconfig.server.json* et *main.server.ts*. Le *tsconfig.server.json* fichier spécifie les options de la compilation TypeScript. Le *main.server.ts* fichier sert de point d’entrée du code pendant SSR.

Ajouter un nouveau fichier appelé *tsconfig.server.json* à l’intérieur de *ClientApp/src* (en même temps qu’existants *tsconfig.app.json*), qui contient les éléments suivants :

[!code-json[](sample/AngularServerSideRendering/ClientApp/src/tsconfig.server.json)]

Ce fichier configure le compilateur AOA d’angulaire rechercher un module appelé `app.server.module`. Ajoutez ceci en créant un nouveau fichier à *ClientApp/src/app/app.server.module.ts* (en même temps qu’existants *app.module.ts*) qui contient les éléments suivants : 

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/app/app.server.module.ts)]

Ce module hérite de votre côté client `app.module` et définit quels modules Angular supplémentaires sont disponibles au cours de SSR.

N’oubliez pas que la nouvelle `ssr` entrée dans *.angular-cli.json* référencé d’un fichier de point d’entrée appelé *main.server.ts*. Vous n’avez pas encore ajouté de ce fichier et êtes maintenant temps de le faire. Créer un nouveau fichier à *ClientApp/src/main.server.ts* (en même temps qu’existants *main.ts*), qui contient les éléments suivants :

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/main.server.ts)]

Code de ce fichier s’ASP.NET Core exécute pour chaque demande lorsqu’il exécute la `UseSpaPrerendering` intergiciel (middleware) que vous avez ajouté à la *démarrage* classe. Il porte sur la réception `params` à partir du code .NET (par exemple, l’URL demandée) et effectuer des appels aux API de SSR angulaire pour obtenir le code HTML résultant. 

Proprement parler, cela est suffisant pour activer SSR en mode de développement. Il est primordial d’effectuer une modification finale afin que votre application fonctionne correctement lors de la publication. Dans main de votre application *.csproj* de fichiers, définissez le `BuildServerSideRenderer` valeur de la propriété `true`:

[!code-xml[](sample/AngularServerSideRendering/AngularServerSideRendering.csproj?name=snippet_EnableBuildServerSideRenderer)]

Cela configure le processus de génération pour exécuter `build:ssr` lors de la publication et de déployer les fichiers SSR sur le serveur. Si vous n’activez pas cette, SSR échoue en production.

Lorsque votre application s’exécute en mode de développement ou de production, le code angulaire restitue préalablement au format HTML sur le serveur. Le code côté client s’exécute normalement.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Passer des données à partir du code .NET dans le code TypeScript

Au cours de SSR, vous pouvez souhaiter transmettre des données de chaque demande à partir de votre application ASP.NET Core dans votre application angulaire. Par exemple, vous pouvez transmettre des informations sur le cookie ou quelque chose de lecture à partir d’une base de données. Pour ce faire, modifiez votre *démarrage* classe. Dans le rappel pour `UseSpaPrerendering`, définissez une valeur pour `options.SupplyData` telle que la suivante :

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

Le `SupplyData` vous permet de rappel transmettre arbitraires, chaque demande, les données JSON sérialisable (par exemple, chaînes, des valeurs booléennes ou des nombres). Votre *main.server.ts* code reçoit comme `params.data`. Par exemple, l’exemple de code précédent passe une valeur booléenne comme `params.data.isHttpsRequest` dans le `createServerRenderer` rappel. Vous pouvez le passer à d’autres parties de votre application en aucune façon pris en charge par angulaire. Par exemple, consultez Comment *main.server.ts* transmet le `BASE_URL` valeur à un composant dont le constructeur est déclaré pour le recevoir.

### <a name="drawbacks-of-ssr"></a>Inconvénients de SSR

Pas toutes les applications tirent parti SSR. Le principal avantage est perçu des performances. Les visiteurs atteindre votre application via une connexion réseau lente ou sur les périphériques mobiles lents voir l’interface utilisateur initiale rapidement, même si elle prend un certain temps pour extraire ou analyser les regroupements de JavaScript. Toutefois, plusieurs SPAs sont principalement utilisés via des réseaux d’entreprise interne rapide sur les ordinateurs rapides dans lequel l’application s’affiche presque instantanément.

En même temps, il existe des inconvénients importants pour l’activation de SSR. Il ajoute une complexité au processus de développement. Votre code doit s’exécuter dans deux environnements différents : côté client et côté serveur (dans un environnement de Node.js appelée à partir de ASP.NET Core). Voici quelques points à l’esprit :

* SSR requiert une installation de Node.js sur vos serveurs de production. C’est automatiquement le cas pour certains scénarios de déploiement, telles que les Services d’application Azure, mais pas pour d’autres, tels que Azure Service Fabric.
* L’activation de la `BuildServerSideRenderer` fait de générer votre *node_modules* active à publier. Ce dossier contient 20 000 fichiers, ce qui augmente le temps de déploiement.
* Pour exécuter votre code dans un environnement de Node.js, il ne peut pas s’appuient sur l’existence d’un navigateur JavaScript APIs spécifiques telles que `window` ou `localStorage`. Si votre code (ou une bibliothèque tierce que vous référencez) tente d’utiliser ces API, vous obtiendrez une erreur au cours de SSR. Par exemple, n’utilisez pas jQuery, car elle référence d’API spécifiques au navigateur dans de nombreux endroits. Pour éviter les erreurs, vous devez éviter de SSR ou éviter l’API ou des bibliothèques spécifiques au navigateur. Vous pouvez encapsuler tous les appels à ces API dans les vérifications pour vous assurer qu’ils ne sont pas appelés au cours de SSR. Par exemple, utiliser un contrôle tel que le suivant dans le code JavaScript ou TypeScript :

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```
