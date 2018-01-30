---
title: "À l’aide de JavaScriptServices pour la création d’Applications à Page unique"
author: scottaddie
description: "En savoir plus sur les avantages de l’utilisation de JavaScriptServices pour créer une Application de Page unique (SPA) est soutenu par ASP.NET Core."
manager: wpickett
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 08/02/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/spa-services
ms.openlocfilehash: bd18d342de7c147e3588bd6daa3aebd68aa81c36
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="using-javascriptservices-for-creating-single-page-applications-with-aspnet-core"></a>À l’aide de JavaScriptServices pour la création d’Applications à Page unique avec ASP.NET Core

Par [Scott Addie](https://github.com/scottaddie) et [Fiyaz Hasan](http://fiyazhasan.me/)

Une Application à Page unique (SPA) est un type d’application web en raison de son expérience utilisateur riche inhérente. Intégration des infrastructures SPA côté client ou les bibliothèques, telles que [angulaire](https://angular.io/) ou [réagir](https://facebook.github.io/react/), avec les infrastructures de côté serveur telles que ASP.NET Core peut être difficile. [JavaScriptServices](https://github.com/aspnet/JavaScriptServices) a été développé afin de réduire la friction dans le processus d’intégration. Il permet à une opération transparente entre les piles de technologie de serveur et de client.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a>Qu’est JavaScriptServices ?

JavaScriptServices est une collection de technologies côté client pour ASP.NET Core. Son objectif est de positionner ASP.NET Core en tant que plateforme par défaut côté serveur développeurs de génération SPAs.

JavaScriptServices se compose de trois packages NuGet distinctes :
* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)
* [Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)

Ces packages sont utiles si vous :
* Exécuter JavaScript sur le serveur
* Utiliser une infrastructure SPA ou la bibliothèque
* Générer des composants côté client avec Webpack

Une grande partie du focus dans cet article est placée sur l’utilisation du package SpaServices.

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a>Qu’est SpaServices ?

SpaServices a été créé pour positionner plateforme par défaut côté serveur les développeurs de génération SPAs ASP.NET Core. SpaServices n’est pas requis pour développer SPAs avec ASP.NET Core, et il ne vous dans une infrastructure client particulier.

SpaServices fournit une infrastructure utile telles que :
* [Pré-rendu du côté serveur](#server-prerendering)
* [Intergiciel (middleware) de Webpack Dev](#webpack-dev-middleware)
* [Remplacement d’un Module à chaud](#hot-module-replacement)
* [Programmes d’assistance de routage](#routing-helpers)

Collectivement, ces composants d’infrastructure améliorent le flux de travail de développement et de l’expérience de l’exécution. Les composants peuvent être adoptées individuellement.

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a>Conditions préalables pour l’utilisation de SpaServices

Pour utiliser SpaServices, vous devez installer les éléments suivants :
* [Node.js](https://nodejs.org/) (version 6 ou version ultérieure) avec npm
    * Pour vérifier que ces composants sont installés et sont accessibles, exécutez la commande suivante à partir de la ligne de commande :

    ```console
    node -v && npm -v
    ```

Remarque : Si vous effectuez un déploiement vers un site web Azure, vous n’avez à faire quoi que ce soit ici &mdash; Node.js est installé et disponible dans les environnements de serveur.

* [Kit de développement .NET core](https://www.microsoft.com/net/download/core) 1.0 (ou version ultérieure)
    * Si vous êtes sous Windows, cela peut être installé en sélectionnant de 2017 Visual Studio **.NET Core le développement multiplateforme** la charge de travail.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) package NuGet

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a>Pré-rendu du côté serveur

Une application (également appelé isomorphes) universelle est une application JavaScript capable d’exécuter à la fois sur le serveur et le client. Angulaire, réagissent et autres infrastructures répandues ; fournit une plateforme universelle pour ce style de développement d’application. L’idée est d’abord restituer les composants framework sur le serveur via Node.js, puis de déléguer davantage d’exécution pour le client.

ASP.NET Core [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro) fournie par SpaServices simplifier l’implémentation de pré-rendu du côté serveur en appelant les fonctions JavaScript sur le serveur.

### <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :
* [ASPNET-pré-rendu](https://www.npmjs.com/package/aspnet-prerendering) package npm :

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a>Configuration

Les programmes d’assistance de balise deviennent détectables via l’inscription de l’espace de noms du projet *_ViewImports.cshtml* fichier :

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Ces programmes d’assistance de balise abstraire les complexités de communiquer directement avec les API de bas niveau en utilisant une syntaxe de type HTML à l’intérieur de la vue Razor :

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a>Le `asp-prerender-module` d’assistance de balise

Le `asp-prerender-module` s’exécute le programme d’assistance de balise, utilisés dans l’exemple de code précédent, *ClientApp/dist/main-server.js* sur le serveur via Node.js. Pour de clarté, *principal-server.js* fichier est un artefact de la tâche de transpilation TypeScript et JavaScript dans les [Webpack](http://webpack.github.io/) processus de génération. Webpack définit un alias de point d’entrée de `main-server`; et, le parcours du graphique de dépendance pour cet alias où commence la *démarrage/ClientApp-server.ts* fichier :

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Dans l’exemple suivant angulaire, le *démarrage/ClientApp-server.ts* fichier utilise le `createServerRenderer` (fonction) et `RenderResult` le type de la `aspnet-prerendering` package npm pour configurer le rendu du serveur via Node.js. Le balisage HTML destiné à rendu côté serveur est passé à un appel de fonction de résolution, qui est encapsulé dans un script JavaScript fortement typée `Promise` objet. Le `Promise` importance de l’objet est qu’elle fournit en mode asynchrone le balisage HTML pour la page pour l’injection dans l’élément d’espace réservé du DOM.

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a>Le `asp-prerender-data` d’assistance de balise

Associée à la `asp-prerender-module` assistance de balise, le `asp-prerender-data` application d’assistance de balise peut servir à transmettre des informations contextuelles à partir de la vue Razor pour le code JavaScript côté serveur. Par exemple, le balisage suivant passe des données utilisateur à la `main-server` module :

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Les données reçues `UserName` argument est sérialisé en utilisant le sérialiseur JSON intégré et est stocké dans le `params.data` objet. Dans l’exemple suivant angulaire, les données sont utilisées pour construire un message d’accueil personnalisé dans un `h1` élément :

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Remarque : Les noms de propriété passés dans les programmes d’assistance de balise sont représentés par **PascalCase** notation. Comparez cela à JavaScript, où les mêmes noms de propriété sont représentés par **camelCase**. La configuration de sérialisation JSON par défaut est responsable de cette différence.

Pour développer l’exemple de code précédent, données peuvent être passées à partir du serveur à la vue par HYDRATATION le `globals` cette propriété est fournie pour le `resolve` fonction :

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Le `postList` tableau défini à l’intérieur de la `globals` objet est attaché au navigateur global `window` objet. Cette levée variable à portée globale d’élimine la duplication des efforts, notamment en ce qui concerne le chargement des données de mêmes qu’une seule fois sur le serveur et de nouveau sur le client.

![variable globale de postList attaché à un objet de fenêtre](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a>Intergiciel (middleware) de Webpack Dev

[Intergiciel (middleware) de Webpack Dev](https://webpack.github.io/docs/webpack-dev-middleware.html) introduit un flux de travail de développement simplifié par laquelle Webpack génère les ressources à la demande. L’intergiciel (middleware) compile automatiquement et fournit des ressources de côté client lorsqu’une page est rechargée dans le navigateur. L’autre approche consiste à appeler manuellement des Webpack via un script de génération du projet npm quand une dépendance tierce ou le code personnalisé change. Un npm générer un script le *package.json* fichier est indiqué dans l’exemple suivant :

[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]

### <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :
* [ASPNET-webpack](https://www.npmjs.com/package/aspnet-webpack) package npm :

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a>Configuration

Intergiciel (middleware) de Webpack Dev est inscrit dans le pipeline de demande HTTP via le code suivant dans le *Startup.cs* du fichier `Configure` méthode :

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]

Le `UseWebpackDevMiddleware` méthode d’extension doit être appelée avant [l’inscription d’hébergement de fichiers statiques](xref:fundamentals/static-files) via la `UseStaticFiles` méthode d’extension. Pour des raisons de sécurité, inscrire l’intergiciel (middleware) uniquement lorsque l’application s’exécute en mode de développement.

Le *webpack.config.js* du fichier `output.publicPath` propriété indique à l’intergiciel (middleware) pour surveiller le `dist` dossier pour les modifications :

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a>Remplacement d’un Module à chaud

Pensez à Webpack [remplacement d’un Module à chaud](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) fonctionnalité (HMR) comme une évolution du [Webpack Dev Middleware](#webpack-dev-middleware). HMR présente les mêmes avantages, mais il simplifie davantage le flux de travail de développement en mettant à jour automatiquement de contenu de la page après la compilation des modifications. Ne pas confondre avec une actualisation du navigateur, ce qui entraînerait une interférence avec l’état en mémoire actuel et la session de débogage de SPA. Il existe un lien direct entre le service de l’intergiciel (middleware) de Webpack développement et le navigateur, ce qui signifie que les modifications sont envoyées au navigateur.

### <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :
* [intergiciel Webpack à chaud](https://www.npmjs.com/package/webpack-hot-middleware) package npm :

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a>Configuration

Le composant HMR doit être inscrit dans le pipeline de demande HTTP de MVC dans le `Configure` méthode :

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

En tant qu’était le cas avec [Webpack Dev Middleware](#webpack-dev-middleware), le `UseWebpackDevMiddleware` méthode d’extension doit être appelée avant la `UseStaticFiles` méthode d’extension. Pour des raisons de sécurité, inscrire l’intergiciel (middleware) uniquement lorsque l’application s’exécute en mode de développement.

Le *webpack.config.js* fichier doit définir un `plugins` de tableau, même si celui-ci est laissé vide :

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Après le chargement de l’application dans le navigateur, onglet de la Console des outils de développement fournit la confirmation de l’activation de HMR :

![Message de connexion de remplacement d’un Module à chaud](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a>Programmes d’assistance de routage

Dans la plupart des SPAs basée sur ASP.NET Core, vous souhaiterez routage côté client en plus du routage du côté serveur. Les systèmes de routage SPA et MVC peuvent travailler indépendamment sans interférence. Il existe, toutefois, les cas à un bord posant défis : identification des réponses HTTP 404.

Considérez le scénario dans lequel un itinéraire sans extension de `/some/page` est utilisé. Supposons que la demande n’à la correspondance un itinéraire côté serveur, mais son modèle ne correspond pas à un itinéraire côté client. Examinons à présent une demande entrante de `/images/user-512.png`, qui généralement s’attend à trouver un fichier image sur le serveur. Si ce chemin d’accès de la ressource demandée ne correspond pas à un itinéraire de côté serveur ou d’un fichier statique, il est peu probable que l’application côté client serait la gérer, il est souvent préférable de retourner un code d’état HTTP 404.

### <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :
* Le package npm de routage côté client. À l’aide d’angulaire par exemple :

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a>Configuration

Une méthode d’extension nommée `MapSpaFallbackRoute` est utilisé dans le `Configure` méthode :

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]

Conseil : Les itinéraires sont évaluées dans l’ordre dans lequel ils sont configurés. Par conséquent, le `default` itinéraire dans l’exemple de code précédent est utilisé pour la recherche de correspondance.

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a>Création d’un projet

JavaScriptServices fournit des modèles d’application préconfigurée. SpaServices est utilisé dans ces modèles, en association avec différentes infrastructures et bibliothèques, telles qu’angulaire, Aurelia, Knockout, réagissent et la Vue.

Ces modèles peuvent être installés via l’interface de ligne de base .NET en exécutant la commande suivante :

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Une liste des modèles SPA disponibles s’affiche :

| Modèles                                 | Nom court | Langue | Balises        |
|:------------------------------------------|:-----------|:---------|:------------|
| MVC ASP.NET Core avec angulaire             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec Aurelia             | aurelia    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec Knockout.js         | Knockout   | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec React.js et réédition  | reactredux | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec Vue.js              | vue        | [C#]     | Web/MVC/SPA | 

Pour créer un nouveau projet à l’aide d’un des modèles de SPA, incluez le **nom court** du modèle dans le `dotnet new` commande. La commande suivante crée une application angulaire avec ASP.NET MVC de base configuré pour le côté serveur :

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a>Définir le mode de configuration du runtime

Il existe deux modes de configuration principal d’exécution :
* **Développement**:
    * Inclut des mappages de sources pour faciliter le débogage.
    * N’Optimisez le code côté client pour les performances.
* **Production**:
    * Exclut les mappages de sources.
    * Optimise le code côté client via une minimisation & de regroupement.

ASP.NET Core utilise une variable d’environnement nommée `ASPNETCORE_ENVIRONMENT` pour stocker le mode de configuration. Consultez  **[définition de l’environnement](xref:fundamentals/environments#setting-the-environment)**  pour plus d’informations.

### <a name="running-with-net-core-cli"></a>En cours d’exécution avec le .NET Core CLI

Restaurer les packages npm NuGet requis en exécutant la commande suivante à la racine du projet :

```console
dotnet restore && npm i
```

Générer et exécuter l’application :

```console
dotnet run
```

Démarrage de l’application sur localhost conformément à la [mode de configuration d’exécution](#runtime-config-mode). Navigation vers `http://localhost:5000` dans le navigateur affiche la page d’accueil.

### <a name="running-with-visual-studio-2017"></a>En cours d’exécution avec Visual Studio 2017

Ouvrez le *.csproj* fichier généré par le `dotnet new` commande. Les packages NuGet et npm nécessaires soient restaurés automatiquement sur le projet ouvert. Ce processus de restauration peut prendre quelques minutes, et l’application est prête à s’exécuter quand elle se termine. Cliquez sur le bouton vert d’exécution ou appuyez sur `Ctrl + F5`, et le navigateur s’ouvre à la page d’accueil de l’application. L’application s’exécute sur localhost conformément à la [mode de configuration d’exécution](#runtime-config-mode). 

<a name="app-testing"></a>

## <a name="testing-the-app"></a>Test de l’application

Modèles de SpaServices sont préconfigurés pour exécuter des tests côté client à l’aide de [Karma](https://karma-runner.github.io/1.0/index.html) et [Jasmine](https://jasmine.github.io/). Jasmine est une unité de populaires infrastructure de test pour JavaScript, alors que Karma est un test runner pour ces tests. KARMA est configuré pour fonctionner avec le [Webpack Dev Middleware](#webpack-dev-middleware) telles que le développeur n’est pas nécessaire d’arrêter et d’exécuter le test chaque fois que des modifications. S’il s’agit du code en cours d’exécution sur le cas de test ou le cas de test lui-même, le test s’exécute automatiquement.

À l’aide de l’application angulaire par exemple, deux cas de test au jasmin sont déjà fournies pour le `CounterComponent` dans les *counter.component.spec.ts* fichier :

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Ouvrez l’invite de commandes à la racine du projet et exécutez la commande suivante :

```console
npm test
```

Le script lance Karma test runner, qui lit les paramètres définis dans le *karma.conf.js* fichier. Parmi d’autres paramètres, le *karma.conf.js* identifie les fichiers de test à exécuter son `files` tableau :

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a>Publication de l’application

Combinant les composants côté client générés et les artefacts ASP.NET Core publiées dans un package prêt à déployer peut s’avérer lourde. Heureusement, SpaServices orchestre ce processus d’ensemble de la publication avec une cible MSBuild personnalisée nommée `RunWebpack`:

[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

La cible MSBuild a les responsabilités suivantes :
1. Restaurez les packages npm
1. Créer une build de production à l’échelle des ressources tierces, côté client
1. Créer une build de production à l’échelle des ressources côté client personnalisées
1. Copier les actifs Webpack généré dans le dossier de publication

La cible MSBuild est appelée lors de l’exécution :

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Docs angulaires](https://angular.io/docs)
